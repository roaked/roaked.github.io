---
title: "Adversarial Multi-Agent RL, BattleMode"
weight: 5
---

# **Adversarial Multi-Agent Reinforcement Learning**

This page documents the **battle mode** implemented in [`agent_RL.py`](https://github.com/roaked/snake-q-learning-genetic-algorithm/blob/main/agent_RL.py), where three controllers are compared in the same experiment:

- 1. **GA-Optimized RL** (DQN + genetic hyperparameter updates)
- 2. **Standard RL** (baseline DQN)
- 3. **Hamiltonian** (deterministic cycle-following policy)

Unlike single-agent training, this setup emphasizes relative performance under shared constraints, direct competition, and robustness over many rounds.

## 1 Multi-Agent Setup

The adversarial environment is built around `SnakeBattleArena`, which places all three snakes in one board and advances them in lockstep each frame.

### 1.1. Arena initialization

The arena tracks per-agent state (`snake`, `direction`, `alive`, `scores`) and global round statistics (`wins`, `round`):

```python
class SnakeBattleArena:
    def __init__(self, width=640, height=640, block_size=20, speed=220):
        self.width = width
        self.height = height
        self.block_size = block_size
        self.speed = speed
        self.wins = {'ga': 0, 'rl': 0, 'ham': 0}
        self.round = 0
        self._reset_round()
```

### 1.2. Per-round spawn state

At the beginning of each round, each model starts from a distinct position and heading so no method receives positional advantage by default:

```python
self.snakes = {
    'ga': [Point(b * 5, b * 5), Point(b * 4, b * 5), Point(b * 3, b * 5)],
    'rl': [Point(self.width - b * 6, b * 5), Point(self.width - b * 5, b * 5), Point(self.width - b * 4, b * 5)],
    'ham': [Point(cx, self.height - b * 6), Point(cx, self.height - b * 5), Point(cx, self.height - b * 4)],
}
self.directions = {'ga': Direction.RIGHT, 'rl': Direction.LEFT, 'ham': Direction.UP}
self.alive = {'ga': True, 'rl': True, 'ham': True}
```

## 2 Shared-Battle Dynamics

Each frame, all alive snakes propose actions, then the arena resolves movement, collisions, rewards, food placement, and round termination.

### 2.1. Joint action step

```python
ga_action = ga_agent.get_action(ga_state_old) if arena.alive['ga'] else [1, 0, 0]
rl_action = rl_agent.get_action(rl_state_old) if arena.alive['rl'] else [1, 0, 0]
ham_action = _hamiltonian_move(arena.get_view('ham'), next_lookup, prev_lookup) if arena.alive['ham'] else [1, 0, 0]
rewards, round_done = arena.step({'ga': ga_action, 'rl': rl_action, 'ham': ham_action})
```

This enforces synchronized progression: each policy acts under the same food location, board state evolution, and frame timing.

### 2.2. Collision and reward rules

The arena penalizes elimination and rewards food capture:

- Collision (wall/body/head conflict): reward `-10`
- Food consumed: reward `+10`
- Safe step without food: reward `0`

Representative logic:

```python
if self.is_collision(snake_id, new_head):
    dead.add(snake_id)
...
if snake_id in dead:
    self.alive[snake_id] = False
    rewards[snake_id] = -10
...
if new_heads[snake_id] == self.food:
    self.scores[snake_id] += 1
    rewards[snake_id] = 10
```

### 2.3. Round termination and winner

A round finishes when one or zero snakes remain alive, or when a frame cap is reached:

```python
alive_ids = [sid for sid, alive in self.alive.items() if alive]
round_done = len(alive_ids) <= 1 or self.frame_iteration > 200
if round_done and len(alive_ids) == 1:
    self.wins[alive_ids[0]] += 1
```

## 3 Model-Specific Behavior Inside Battle

### 3.1. Standard RL agent

The baseline RL snake follows the same DQN update cycle as in the single-agent setting:

```python
rl_state_old = rl_agent.get_state(rl_view_old)
rl_action = rl_agent.get_action(rl_state_old)
...
rl_agent.train_short_memory(rl_state_old, rl_action, rewards['rl'], rl_state_new, rl_done)
rl_agent.remember(rl_state_old, rl_action, rewards['rl'], rl_state_new, rl_done)
```

This gives a direct baseline for learned behavior under adversarial pressure.

### 3.2. GA-optimized RL agent

The GA-controlled snake also learns via DQN, but periodically updates training hyperparameters using genetic search:

```python
_, best_params, _ = ga_genetic.genetic(
    NUM_GENERATIONS,
    score=ga_score,
    record=ga_record,
    steps=arena.frame_iteration,
    collisions=0,
    same_positions_counter=0,
    game_metrics_list=ga_metrics
)
ga_agent.update_hyperparameters(ga_genetic.get_current_parameters())
if completed_rounds % 20 == 0 and isinstance(best_params, dict):
    ga_agent.update_hyperparameters(best_params)
```

This allows GA-RL to adapt exploration and learning behavior while competing.

### 3.3. Hamiltonian controller

The Hamiltonian snake does not train; it computes deterministic moves from cycle neighbors:

```python
ham_action = _hamiltonian_move(arena.get_view('ham'), next_lookup, prev_lookup)
```

Its strength is consistency and survival; its weakness is reduced tactical flexibility for short-term food races.

## 4 Training and Evaluation Flow

At round end, both learning agents perform long-memory updates, while Hamiltonian only resets game state:

```python
if round_done:
    completed_rounds += 1
    ga_agent.n_games += 1
    rl_agent.n_games += 1
    ga_agent.train_long_memory()
    rl_agent.train_long_memory()
    ...
    arena._reset_round()
```

This structure supports two key measurements:

- **Win rate** (`arena.wins`) as direct adversarial outcome
- **Score trends** per model for efficiency and consistency

## 5 Running the Battle Modes

### 5.1. Same-board adversarial battle (direct interaction)

```bash
python agent_RL.py battle
```

Aliases supported by the CLI: `arena`, `fight`, `vs`.

### 5.2. Side-by-side comparison mode (parallel, non-interacting boards)

```bash
python agent_RL.py compare
```

Alias: `all3`.

## 6 Interpretation Notes

When reading results, compare all three dimensions together:

- **Wins**: who survives and closes rounds.
- **Score**: who collects food most effectively.
- **Stability**: variance across many rounds/games.

In practice, this adversarial page complements the single-model analysis by showing how each policy behaves when exposed to a more competitive and dynamic evaluation protocol.
