---
title: "Micromouse: Flood Fill and A*"
weight: 4
---


# AI Pioneer Debut: Maze SLAM

[According to many sources](https://webmuseum.mit.edu/detail.php?module=objects&type=related&kv=76066), "the first system (...) is the Theseus. It was built by *Claude Shannon* in 1950 and was a remote-controlled mouse that was able to find its way out of a labyrinth and could remember its course.  In seven decades, the abilities of artificial intelligence have come a long way.


## **1) Simulation Driver (`MicromouseApp._tick`)**

The simulation loop is implemented inside `_tick()` and scheduled using `tkinter`:

```python
def _tick(self) -> None:
    if self.mode == "xyz":
        if not self.flood3d.done:
            self.flood3d.step()
        if self.astar3d.status == "searching":
            for _ in range(self.astar_expansions):
                self.astar3d.step()
                if self.astar3d.status != "searching":
                    break
        elif self.astar3d.status == "found" and self.astar3d_anim_index < len(self.astar3d.path) - 1:
            self.astar3d_anim_index += 1
            self.astar3d_runner_cell = self.astar3d.path[self.astar3d_anim_index]
    else:
        if not self.flood.done:
            self.flood.step()
        if self.astar.status == "searching":
            for _ in range(self.astar_expansions):
                self.astar.step()
                if self.astar.status != "searching":
                    break
        elif self.astar.status == "found" and self.astar_anim_index < len(self.astar.path) - 1:
            self.astar_anim_index += 1
            self.astar_runner_cell = self.astar.path[self.astar_anim_index]
    self._draw_floodfill()
    self._draw_astar()
    self._update_info()
    self.root.after(self.tick_ms, self._tick)
```

### What this does, precisely

- Floodfill explorer advances by **one step per frame**.
- A* can expand **multiple nodes per frame** (`--astar-expansions`).
- After planning, both panels are redrawn and next frame is queued using `after(...)`.

## **2) Maze Representation and Generation**

### 2D (`class Maze`)

- `walls[(r,c)]` stores `N/E/S/W` booleans.
- `neighbor(...)`, `has_wall(...)`, and `can_move(...)` are movement primitives.
- `goal_cells()` returns center cell(s): 1 center for odd grids, 4 centers for even grids.

Generation is DFS maze carving + random extra openings:

```python
start = (0, 0)
stack = [start]
visited = {start}
while stack:
    current = stack[-1]
    candidates = [(nxt, d) for nxt, d in self.neighbors(current) if nxt not in visited]
    if not candidates:
        stack.pop()
        continue
    nxt, d = self.rng.choice(candidates)
    self._remove_wall(current, nxt, d)
    visited.add(nxt)
    stack.append(nxt)
```

Then it injects extra openings:

```python
extra_openings = max(1, (self.size * self.size) // 12)
```

### 3D (`class Maze3D`)

- Extends walls to `N/E/S/W/U/D`.
- Cells are indexed as `(z, r, c)`.
- `goal_cell()` is the middle of the top layer: `(levels - 1, mid, mid)`.
- Uses the same DFS-style carving logic across 3D neighbors + random extra openings.

## **3) Floodfill Explorer Internals**

### `FloodFillExplorer` (2D)

State model:

- `known[cell][dir]` in `{UNKNOWN, OPEN, WALL}`.
- Boundary walls are initialized as known walls.
- `distances` is recomputed by reverse BFS from goal set.

Core update:

```python
def step(self) -> None:
    if self.done:
        return
    self._sense_cell(self.position)
    self.distances = self._compute_distances()
    if self.position in self.goals:
        self.done = True
        return
    nxt = self._pick_next_cell()
    if nxt is None:
        self.done = True
        return
    self.position = nxt
    self.trail.append(nxt)
    self.steps += 1
    if self.steps >= self.max_steps:
        self.done = True
```

Decision policy in `_pick_next_cell()`:

- candidate neighbors must be `OPEN` in `known`.
- chooses minimum of `(distance_from_floodfill, goal_manhattan)` for tie-breaking.

### `FloodFillExplorer3D`

- same pattern in XYZ space with Manhattan distance in 3 axes.
- step budget scales with volume:

```python
self.max_steps = self.maze.size * self.maze.size * self.maze.levels * 10
```

## **4) A* Runner Internals**

### `AStarRunner` (2D)

- Priority queue stores `(f_score, counter, node)` for deterministic tie handling.
- Uses:
  - `open_heap` (heapq frontier)
  - `open_set` (fast membership)
  - `closed`
  - `came_from`, `g_score`

Expansion step:

```python
for _, _, d in DIRS:
    if self.maze.has_wall(node, d):
        continue
    nxt = self.maze.neighbor(node, d)
    if nxt is None or nxt in self.closed:
        continue
    trial = self.g_score[node] + 1
    if trial < self.g_score.get(nxt, 10**9):
        self.came_from[nxt] = node
        self.g_score[nxt] = trial
        f_score = trial + self._heuristic(nxt)
        heapq.heappush(self.open_heap, (f_score, self._counter, nxt))
        self.open_set.add(nxt)
```

Heuristic:

```python
min(abs(cell[0]-g[0]) + abs(cell[1]-g[1]) for g in self.goals)
```

### `AStarRunner3D`

- exactly the same engine in 3D:

```python
min(abs(cell[0]-g[0]) + abs(cell[1]-g[1]) + abs(cell[2]-g[2]) for g in self.goals)
```

## **5) App Initialization: How Components Wire Together**

In `MicromouseApp.__init__`:

- in `xyz` mode:
  - builds `Maze3D`
  - `start3d = (0, size - 1, 0)`
  - goal set uses `maze3d.goal_cell()`
  - creates `FloodFillExplorer3D` + `AStarRunner3D`
- in `2d/3d` modes:
  - builds `Maze`
  - `start = (size - 1, 0)`
  - goals use `maze.goal_cells()`
  - creates `FloodFillExplorer` + `AStarRunner`

This is exactly where the simulator decides which state machine and renderer will be active.

## **6) Rendering and Instrumentation**

The app renders two synchronized panels:

- left: Floodfill exploration status
- right: A* search status + animated final path

Important render helpers:

- `_draw_floodfill_2d`, `_draw_astar_2d`
- `_draw_floodfill_3d`, `_draw_astar_3d` (isometric blocks)
- `_draw_floodfill_xyz`, `_draw_astar_xyz` (layered Z slices with up/down arrows)
- `_update_info` (live status line: steps, goal reached, nodes explored)

## **7) CLI Entry Points**

The runtime surface is explicit in `parse_args()` + `main()`:

```python
p.add_argument("--mode", choices=("2d", "3d", "xyz"), default="2d")
p.add_argument("--astar-expansions", type=int, default=2)
p.add_argument("--levels", type=int, default=4)
...
app = MicromouseApp(...)
app.run()
```

This means your “simulation function” from CLI is effectively:

1. parse options
2. construct `MicromouseApp`
3. enter `_tick` loop through `run()`

{{< hint important >}}
- If behavior looks wrong, debug in this order: `Maze/Maze3D` generation → `FloodFillExplorer.step` / `AStarRunner.step` → `MicromouseApp._tick` scheduling.
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

