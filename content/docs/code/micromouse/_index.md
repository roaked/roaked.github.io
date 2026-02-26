---
title: "Micromouse: Flood Fill and A*"
weight: 4
---


# AI Pioneer Debut: Maze SLAM

<div style="display: flex; justify-content: space-between; gap: 10px; align-items: center;">
  
  <video width="100%" height="auto" controls autoplay loop muted playsinline>
    <source src="/videos/first_run_astar.webm" type="video/webm">
    First Run Slam.
  </video>

</div>

## **1. Simulation Driver (`MicromouseApp._tick`)**

The simulation loop is implemented inside `_tick()` and scheduled using `tkinter`:

```python
def _tick(self) -> None:
    self.frame_count += 1
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
    self._update_animation_state()
    if self.frame_count % self.render_every == 0:
        self._draw_visualizations()
        self._update_info()
    self.root.after(self.tick_ms, self._tick)
```

### What this does, precisely

- Floodfill advances by **one exploration step per frame**.
- A* expands **multiple nodes per frame** (`--astar-expansions`).
- The mouse markers are interpolated via `_update_animation_state()` for smoother motion.
- Rendering is throttled by `render_every`, then the next frame is queued with `after(...)`.

## **2. Maze Representation and Generation**

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

## **3. Floodfill Explorer Internals**

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

Step budget in the current repo implementation:

```python
self.max_steps = self.maze.size * self.maze.size * 8
```

## **4. A* Runner Internals**

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
        self._counter += 1
        f_score = trial + self._heuristic(nxt)
        heapq.heappush(self.open_heap, (f_score, self._counter, nxt))
        self.open_set.add(nxt)
```

Heuristic:

```python
min(abs(cell[0]-g[0]) + abs(cell[1]-g[1]) for g in self.goals)
```

## **5. Results**

<div style="display: flex; justify-content: space-between; gap: 10px; align-items: center;">
  
  <video width="100%" height="auto" controls autoplay loop muted playsinline>
    <source src="/videos/first_run_astar.webm" type="video/webm">
    First Snake.
  </video>

</div>

{{< hint important>}}
**Used References:** 
{{< /hint >}}

- [roaked/micromouse (source repository)](https://github.com/roaked/micromouse)

- [System design for the robot (extracted from Gal Yaroslavsky)](http://mbed.org/users/goy5022/code/MicroMouse-v1/file/ce5b1bf38077/Headers/Mapping.h)

- [Maze Simulator from Google](https://code.google.com/p/maze-solver/)

- [Image Processing Implementation coupled with Graph Theory algo's (interesting paper!)](https://www.researchgate.net/publication/321121617_Intelligent_Maze_Solving_Robot_Based_on_Image_Processing_and_Graph_Theory_Algorithms)

- [Flood-Fill Algorithm Geeks4Geeks](https://www.geeksforgeeks.org/flood-fill-algorithm/)

- [Flood-Fill Implementation in C / Arduino](https://github.com/adam2392/ieee_micromouse)

- [A* Algorithm Geeks4Geeks](https://www.geeksforgeeks.org/a-search-algorithm/)

- Micromouse '24 -- 17/18 February

