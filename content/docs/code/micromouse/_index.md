---
title: "Micromouse: Flood Fill and A*"
weight: 4
---


# AI Pioneer Debut: Maze SLAM

[According to many sources](https://webmuseum.mit.edu/detail.php?module=objects&type=related&kv=76066), "the first system (...) is the Theseus. It was built by *Claude Shannon* in 1950 and was a remote-controlled mouse that was able to find its way out of a labyrinth and could remember its course.  In seven decades, the abilities of artificial intelligence have come a long way.

## **Code References (GitHub Methods)**

- [Repository root](https://github.com/roaked/micromouse)
- [Search: simulation functions](https://github.com/roaked/micromouse/search?q=simulation&type=code)
- [Search: Flood Fill functions](https://github.com/roaked/micromouse/search?q=flood+fill&type=code)
- [Search: A* functions](https://github.com/roaked/micromouse/search?q=astar&type=code)
- [Search: map/wall update methods](https://github.com/roaked/micromouse/search?q=wall+update&type=code)
- [Search: step/move methods](https://github.com/roaked/micromouse/search?q=step+move&type=code)

{{< hint note >}}
- Use the links above as jump points to bind each section below to concrete methods as the repository evolves.
{{< /hint >}}

## **1) Purpose of the Simulation Function**

The simulation function is the runtime coordinator that executes one complete micromouse episode:

1. initialize maze, robot pose, and planner state
2. run repeated sensing/mapping/planning/control updates
3. stop on goal or failure conditions
4. return metrics for evaluation and debugging

In practice, this function is the "single source of truth" for experiment reproducibility because every policy and planner goes through the same loop contract.

## **2) Expected Function Contract**

A typical simulation signature follows this shape:

```python
def run_simulation(
    maze,
    planner,
    start=(0, 0),
    goal_cells=((7, 7), (7, 8), (8, 7), (8, 8)),
    max_steps=10_000,
    random_seed=0,
):
    ...
```

### Inputs

- `maze`: ground-truth wall structure used by sensor emulation
- `planner`: route generator (`flood_fill`, `a_star`, or hybrid)
- `start`: initial robot cell and heading
- `goal_cells`: target center cells
- `max_steps`: hard cap to prevent infinite loops

### Outputs

The function should return rich diagnostics, not only success/fail:

```python
{
    "reached_goal": bool,
    "steps": int,
    "path": list[tuple[int, int]],
    "replans": int,
    "collisions": int,
    "visited_cells": int,
    "time_ms": float,
}
```

## **3) Per-Step Simulation Pipeline**

At each simulation timestep, the function should apply the same deterministic order:

1. **sense** nearby walls/corridors
2. **update** internal occupancy/wall map
3. **plan** next move or path prefix
4. **validate** the action (no wall crossing)
5. **execute** movement and heading update
6. **log** metrics for post-run analysis

Representative loop skeleton:

```python
for t in range(max_steps):
    local_obs = sense_walls(maze_truth, state.pose)
    state.map = update_internal_map(state.map, state.pose, local_obs)

    if planner.name == "flood_fill":
        cost_map = compute_flood_costs(state.map, goal_cells)
        action = select_best_neighbor(state.pose, cost_map, state.map)
    else:  # A*
        path = a_star_path(state.map, state.pose.cell, goal_cells)
        action = path_to_action(state.pose, path)

    if action is None:
        state.status = "stuck"
        break

    state.pose = apply_action(state.pose, action)
    state.path.append(state.pose.cell)
```

## **4) Mapping Layer: Why It Matters**

Simulation quality depends on strict separation between:

- **ground truth maze** (never directly leaked to planner)
- **discovered map** (incrementally built from sensor data)

This prevents "oracle behavior" and keeps evaluation realistic.

Core responsibilities in map update methods:

- synchronize opposite walls between adjacent cells
- mark visited cells and heading-specific observations
- reject contradictory updates with assertion checks
- keep grid indexing conventions consistent (`row,col` vs `x,y`)

## **5) Planning Layer: Flood Fill and A\***

### Flood Fill usage in simulation

- recompute (or incrementally update) cost-to-go map
- prefer neighbors with strictly smaller potential
- tie-break with heading continuity to reduce turns

```python
cost[next_cell] = min(cost[current] + 1 for current in open_neighbors(next_cell))
```

### A* usage in simulation

- plan shortest discovered path on current map
- apply admissible heuristic (Manhattan distance on grid)
- re-plan whenever new wall evidence invalidates path

```python
f(n) = g(n) + h(n),  where  h(n) = |x_n - x_goal| + |y_n - y_goal|
```

## **6) Controller + Safety Checks**

Before state transition, the simulation function should enforce:

- wall-crossing guard
- bounds check
- repeated-state/stagnation detection

```python
if crosses_wall(state.map, state.pose, action):
    metrics["collisions"] += 1
    state.status = "invalid_action"
    break
```

These checks make planner bugs visible early and avoid silent drift.

## **7) Termination Conditions**

A robust simulation function terminates on:

- goal reached (`pose.cell in goal_cells`)
- step budget exhausted
- invalid action/collision
- planner returns no feasible move
- stagnation threshold exceeded

```python
if state.pose.cell in goal_cells:
    state.status = "success"
    break
if t >= max_steps - 1:
    state.status = "timeout"
```

## **8) Metrics You Should Always Record**

- `steps_to_goal`
- `turn_count`
- `replan_count`
- `unique_cells_visited`
- `dead_end_entries`
- `wall_update_count`

These metrics are essential for comparing planner variants, map update strategies, and heuristic tuning.

## **9) Instrumented Full Example**

```python
def run_simulation(maze_truth, planner, start, goal_cells, max_steps=10_000):
    state = init_state(start=start)
    metrics = init_metrics()

    for t in range(max_steps):
        obs = sense_walls(maze_truth, state.pose)
        update_ok = update_internal_map(state.map, state.pose, obs)
        if not update_ok:
            state.status = "map_error"
            break

        action = planner.next_action(state.map, state.pose, goal_cells)
        if action is None:
            state.status = "no_path"
            break

        if crosses_wall(state.map, state.pose, action):
            metrics["collisions"] += 1
            state.status = "invalid_action"
            break

        state.pose = apply_action(state.pose, action)
        update_metrics(metrics, state)

        if state.pose.cell in goal_cells:
            state.status = "success"
            break
    else:
        state.status = "timeout"

    return build_result(state, metrics)
```

{{< hint important >}}
- The simulation loop is the critical integration point: sensing, mapping, planning, and control quality all surface here. If your results look unstable, debug this function first.
{{< /hint >}}



{{< hint important>}}
**Used References:** 
{{< /hint >}}

- [System design for the robot (extracted from Gal Yaroslavsky)](http://mbed.org/users/goy5022/code/MicroMouse-v1/file/ce5b1bf38077/Headers/Mapping.h)

- [Maze Simulator from Google](https://code.google.com/p/maze-solver/)

- [Image Processing Implementation coupled with Graph Theory algo's (interesting paper!)](https://www.researchgate.net/publication/321121617_Intelligent_Maze_Solving_Robot_Based_on_Image_Processing_and_Graph_Theory_Algorithms)

- [Flood-Fill Algorithm Geeks4Geeks](https://www.geeksforgeeks.org/flood-fill-algorithm/)

- [Flood-Fill Implementation in C / Arduino](https://github.com/adam2392/ieee_micromouse)

- [A* Algorithm Geeks4Geeks](https://www.geeksforgeeks.org/a-search-algorithm/)

- Micromouse '24 -- 17/18 February

