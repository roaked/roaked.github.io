---
title: "BaseRL, GA-RL & Hamiltonian"
weight: 2
---

# **Off Policy and DQN Parameter Optimization**

![sidas](https://miro.medium.com/v2/resize:fit:1200/1*zRZ46MeFZMd5F52CHM6EYA.png)

This section documents the [`snake-q-learning-genetic-algorithm`](https://github.com/roaked/snake-q-learning-genetic-algorithm) project: a Snake environment built with `pygame` and trained with an off-policy Deep Q-Network (DQN), with genetic search used to tune key learning parameters.

The implementation is organized around four core components:

- 1. `game.py` / `game_user.py`: environment dynamics, collisions, food placement, and rendering.
- 2. `model.py`: Q-network definition (`LinearQNet`) and training loop wrapper (`QTrainer`).
- 3. `agent.py`: epsilon-greedy action selection, replay-based training, and game-loop integration.
- 4. `genetic.py`: parameter optimization over learning-rate, discount, dropout, and exploration ranges.

The goal is to maximize score while keeping training stable, then compare baseline RL behaviour against GA-tuned configurations under the same Snake rules.

## 1 Reinforcement Deep Q-Network Architecture

DQN (Deep Q-Network) stands as a RL algorithm rooted in deep learning principles. It integrates a Q-Learning algorithm with a deep neural network to address RL challenges in expansive state and action spaces. In the DQN algorithm, a neural network is employed to approximate the Q function, where states and actions serve as inputs, yielding corresponding Q values as outputs. Essentially, the DQN algorithm can be broken down into the following steps:

- 1. **Initialization**: Initialize a neural network with random weights θ to approximate the Q function Q(s,a,θ).

- 2. **Experience Replay**: Maintain a replay memory buffer {{< katex >}}\mathcal{D}{{< /katex >}} to store experiences {{< katex >}}e_t = (s_t, a_t, r_t, s_{t+1}){{< /katex >}} consisting of state-action pairs at state t, rewards, and subsequent states encountered during interactions with the environment.

- 3. **Action Selection**: Utilize an epsilon-greedy policy to select actions based on the current state, balancing between exploration and exploitation.

{{< katex display >}}
a_t = \begin{cases} 
\text{random action} & \text{with probability } \epsilon \\
\text{argmax}_a Q(s_t, a, \theta) & \text{otherwise}
\end{cases}
{{< /katex >}}

- 4. **Training**: Periodically sample batches of experiences from the replay memory buffer to update the neural network's parameters. The loss function used for training is typically derived from the Bellman equation, aiming to minimize the discrepancy between the predicted Q values and the target Q values.

{{< katex display >}}
\mathcal{L}(\theta) = \mathbb{E}_{(s, a, r, s') \sim U(D)} \left[ \left( r + \gamma \max_{a'} Q(s', a'; \theta^-) - Q(s, a, \theta) \right)^2 \right]
{{< /katex >}}

- 5. **Repeat**: Iterate step 3 and 4 until convergence, continually updating the neural network to better approximate the Q function.

Overall, the DQN algorithm leverages deep learning techniques to efficiently handle RL problems characterized by large state and action spaces, enabling effective learning and decision-making in complex environments.

### 1.1. LinearQNet and QTrainer Classes

The [LinearQNet](https://github.com/roaked/snake-q-learning-genetic-algorithm/blob/main/model.py) class represents a simple neural network architecture tailored for Q-value approximation in RL. It consists of two linear layers initialized during instantiation, with the first layer transforming input features to a hidden layer and the second layer producing Q-values for different actions. Additionally, it sets up other optional components, such as dropout regularization or weight initialization techniques, aiming to enhance the network's learning process and generalization ability.

```python
def __init__(self, input_size, hidden_size, output_size):
    super().__init__()
    self.linear1 = nn.Linear(input_size, hidden_size)
    self.linear2 = nn.Linear(hidden_size, output_size)
    self.dropout = nn.Dropout(0.2)  # Example: Adding dropout with p=0.2

    # Initialize weights using Xavier initialization
    nn.init.xavier_uniform_(self.linear1.weight)
    nn.init.xavier_uniform_(self.linear2.weight)
```

The forward method defines the forward pass, applying a rectified linear unit (ReLU) activation to the hidden layer's output before generating the Q-values. Additionally, the save method facilitates saving the model's state dictionary `self.state_dict()` to a specified file path using PyTorch's `torch.save` functionality, ensuring the preservation of trained model parameters.

```python
def forward(self, x):
        x = F.relu(self.linear1(x))
        x = self.dropout(x)  # Applying dropout
        x = self.linear2(x)
        return x

def save(self, file_name='model.pth'):
    model_folder_path = './model'
    if not os.path.exists(model_folder_path):
        os.makedirs(model_folder_path)

    file_name = os.path.join(model_folder_path, file_name)
    torch.save(self.state_dict(), file_name)    
```

Meanwhile, the QTrainer class manages the training process for the Q-network. Its initialization configures key parameters like learning rate and discount factor, along with setting up an Adam optimizer to update the model's parameters based on the provided learning rate.

```python
class QTrainer:
    def __init__(self, model, lr, gamma, target_update_freq=1000):
        self.lr = lr
        self.gamma = gamma
        self.model = model
        self.optimizer = optim.Adam(model.parameters(), lr=self.lr)
        self.criterion = nn.MSELoss()
```

This can integrated in the train_step function which orchestrates the neural network training process by executing a single iteration of Q-learning updates. It receives an experience tuple containing information about a state, action, received reward, the resulting next state, and an indicator of whether the episode has ended. The function begins by converting these components into `PyTorch` tensors to facilitate computation within the neural network. Using the provided state, the neural network predicts Q-values for different actions, storing these predictions as `pred`. Now, we should take a look at how a new Q-value for a state-action pair based on the previous Q-value and the received reward, plus the discounted maximum Q-value achievable in the resulting state is computed. The process for the agent to adjust Q-values iteratively, gradually converging towards optimal action-selection strategies by learning from experiences obtained while interacting with the environment can be seen below for the Q-learning update rule.

{{< katex display>}}
Q(s,a) = Q(s,a) + \alpha (r + \gamma \cdot max_{a'} Q(s',a')- Q(s,a))
{{< /katex>}}

Q(s,a) represents the Q-value, indicating the expected cumulative reward by taking action `a` in state `s`. It quantifies the agent's understanding of the long-term desirability of choosing action `a` while in state `s`. The evaluation on whether to overwrite old information during upcoming Q-updates is given by the variable {{< katex >}}\alpha{{< /katex>}}. Higher learning rate ({{< katex >}}\alpha{{< /katex>}}) gives more weight to recent experiences, influencing how much the Q-value changes based on the new piece of information. The agent gets an immediate reward `r` gained for the action after executing action `a` at this current state `s`. This immediate feedback guides the agent's learning, impacting the adjustment of Q-values based on the obtained rewards in each state-action pair. Moreover, the significance of future rewards compared to current rewards is given by {{< katex >}}\gamma{{< /katex>}}. Naturally higher discount factor {{< katex >}}\gamma{{< /katex>}} prioritizes long-term rewards more. Lastly, Q(s',a') indicates the maximum Q-value achievable in the subsequent state `s'` when taking an action `a'`. It influences agent's decision-making by taking into account potential future rewards based on the best action available from the next state, guiding him to more rewarding states.

Employing the Q-learning update rule, the function computes target Q-values based on the predicted values, updating them according to the received rewards and the next state's Q-values if the episode hasn't terminated. Subsequently, it calculates the Mean Squared Error (MSE) loss between the predicted and target Q-values and performs backpropagation to derive gradients for updating the neural network's parameters. Finally, the optimizer applies these gradients to adjust the model's weights through optimization, enhancing the network's ability to approximate accurate Q-values for efficient decision-making in RL scenarios.

```python
def train_step(self, state, action, reward, next_state, done):
    state = torch.tensor(state, dtype=torch.float)
    next_state = torch.tensor(next_state, dtype=torch.float)
    action = torch.tensor(action, dtype=torch.long)
    reward = torch.tensor(reward, dtype=torch.float)

    pred = self.model(state)
    target = pred.clone()

    # Q-learning update rule
    # Handling single-dimensional state and action tensors
    if state.dim() == 1:  # (1,x)
        state = state.unsqueeze(0)
        next_state = next_state.unsqueeze(0)
        action = action.unsqueeze(0)
        reward = reward.unsqueeze(0)
        done = (done,)

    # Predicting Q-values based on current state-action pair
    pred = self.model(state)

    # Clone the prediction for updating
    target = pred.clone()
    for idx in range(len(done)):
        Q_new = reward[idx]
        if not done[idx]:
            Q_new = reward[idx] + self.gamma * torch.max(self.model(next_state[idx]))

        action_idx = torch.argmax(action[idx]).item()
        target[idx][action_idx] = Q_new

    # Zero the gradients, compute loss, backpropagate, and update weights
    self.optimizer.zero_grad()
    loss = self.criterion(target, pred)
    loss.backward()
    self.optimizer.step()
```

Together, these classes form the backbone of a Q-learning approach, where the LinearQNet acts as the neural network to estimate Q-values and the QTrainer orchestrates the training process by updating the network's parameters to improve Q-value predictions.

{{< hint warning>}}
Modifications:
- Experiment with deeper networks, different activation functions (Sigmoid, i.e), regularization L2/ Dropout to prevent overfitting (getting stuck) and use different optimizers (RMSprop or SGD) for `LinearQClass`.
- It is possible to implement different loss functions or Double Q-learning to mitigate bias impact using separate networks to update Q-values.
- Explore how to improve learning efficiency of the agent.
{{< /hint>}}

### 1.2. Deploying a Reinforcement Learning Agent

Let us start by applying the constructor method (__init__) of a class. This is where several fundamental attributes and objects are initialized for a RL agent.

```python
def __init__(self):
        self.n_games = 0 # Number of games played
        self.epsilon = 0 # Parameter for exploration-exploitation trade-off
        self.gamma = 0.9 # Discount factor for future rewards
        self.memory = deque(maxlen=MAX_MEMORY) # Replay memory for storing experiences
        # QTrainer for model training 
        self.trainer = QTrainer(self.model, lr=ALPHA, gamma=self.gamma) 
```

`self.n_games` tracks the number of games the agent has played. `self.epsilon` represents a parameter essential for the [exploration-exploitation trade-off](https://en.wikipedia.org/wiki/Exploration-exploitation_dilemma), influencing the agent's decision-making process. The `self.gamma` variable signifies the discount factor applied to future rewards, impacting the agent's prioritization of immediate versus delayed rewards. The `deque` named `self.memory`, constrained by `MAX_MEMORY`, functions as a replay memory, storing previous experiences crucial for the agent's learning process. Additionally, the `self.trainer`, instantiated as `QTrainer`, facilitates model training using the `self.model`, employing the provided learning rate (`ALPHA`) and discount factor (`gamma`) within the `QTrainer` class.

```python
# Neural network model (input size, hidden layer no. neurons, output size)
self.model = LinearQNet(11, 256, 3) 
self.target_model = LinearQNet(11, 256, 3)
```

The `self.model` is a neural network structure defined as `LinearQNet(11, 256, 3)` indicates specific architectural details tailored for a RL task. The choice of these parameters signifies the design of the neural network for this particular problem domain.

The '11' in the network signifies the input size, representing the number of features or variables characterizing the environment's state, which encompass details directions: vertical and horizontal distance, dangers based on moving forward, left or right and snake and food locations. The '256' hidden units denote the number of neurons in the hidden layer. This specific number, 256, is a heuristic or empirical choice commonly used across different domains: ranging from generative Convolutional Neural Networks (CNNs) architectures such as AlexNet or VGG to Natural Language Processing (NLPs) Recurrent Neural Network (RNNs) architectures. It provides a moderately sized layer that offers sufficient capacity for learning complex representations from the input data without overly increasing computational costs. Additionally, being a [power of two, it aligns well with computational optimizations and hardware implementations](https://stackoverflow.com/questions/63515846/in-neural-networks-why-conventionally-set-number-of-neurons-to-2n), making it computationally efficient for many systems. Lastly, the '3' as the output size corresponds to the number of actions the agent can undertake in the environment. In this case, having three outputs suggests that the agent has three distinct possible actions it can choose from in response to a given state in the environment. For instance, these actions represent movements such as "left", "right" and "forward" seen previously in the development of `game.py`.

```python
def get_state(self, game):
    # Extracting snake's head position and defining points in different directions
    head = game.snake[0]
    point_l = Point(head.x - 20, head.y)
    point_r = Point(head.x + 20, head.y)
    point_u = Point(head.x, head.y - 20)
    point_d = Point(head.x, head.y + 20)
    
    # Determining the snake's current direction
    dir_l = game.direction == Direction.LEFT
    dir_r = game.direction == Direction.RIGHT
    dir_u = game.direction == Direction.UP
    dir_d = game.direction == Direction.DOWN

    # Checking for potential dangers in different directions
    danger_straight = (dir_r and game.is_collision(point_r)) or \
                      (dir_l and game.is_collision(point_l)) or \
                      (dir_u and game.is_collision(point_u)) or \
                      (dir_d and game.is_collision(point_d))

    # Indicating the snake's movement direction
    move_direction = [int(dir_l), int(dir_r), int(dir_u), int(dir_d)]

    # Determining food's relative position compared to the snake's head
    food_position = [
        game.food.x < game.head.x,  # food left
        game.food.x > game.head.x,  # food right
        game.food.y < game.head.y,  # food up
        game.food.y > game.head.y   # food down
    ]

    # Constructing the state representation
    state = [
        int(danger_straight),  # Danger straight
        int(dir_r), int(dir_l), int(dir_u), int(dir_d),  # Move direction
    ] + food_position  # Food location

    return np.array(state, dtype=int)
```

The `get_state` function within the code constructs a comprehensive representation of the game state in the Snake environment. It begins by extracting vital information such as the snake's head position and defining points in multiple directions to detect potential dangers, which include positions 20 units away in various directions. The function then derives the snake's current direction by comparing it with predefined directional indicators (left, right, up, down) based on the game's orientation. It proceeds to assess the presence of **potential dangers in the straight, right, and left directions** by checking for collisions with specific points relative to the snake's current orientation. Furthermore, binary flags are employed to indicate the snake's movement direction, while the relative position of the food compared to the snake's head (left, right, up, down) is determined. Finally, all these features are flushed into an array that serves as a numeric representation of the game state. 


```python
def get_action(self, state):
        # Select actions based on an epsilon-greedy strategy
        self.epsilon = 80 - self.n_games
        final_move = [0,0,0]
        if random.randint(0, 200) < self.epsilon:
            move = random.randint(0, 2)
            final_move[move] = 1
        else:
            state0 = torch.tensor(state, dtype=torch.float)
            prediction = self.model(state0)
            move = torch.argmax(prediction).item()
            final_move[move] = 1

        return final_move
```

The `get_action` method operates as the decision-maker, employing an [epsilon-greedy strategy](https://medium.com/@gridflowai/part-2-in-depth-exploration-on-epsilon-greedy-algorithm-2b19e59bbe22) to balance exploration and exploitation. This strategy dynamically adjusts the agent's behaviour by modifying the exploration rate (`epsilon`) based on the number of games played (`n_games`). If a randomnly generated value falls below the epsilon threshold, indicating exploration, the agent randomly selects an action from the available choices (**move left, right, or straight**). Conversely, in the exploitation phase, when the generated value surpasses the epsilon threshold, the agent exploits its learned knowledge. It leverages its neural network model (`self.model`) to predict Q-values for each potential action given the current state, selecting the action with the highest predicted Q-value. The resulting one-hot encoded representation (`final_move`) denotes the chosen action, guiding the agent's movement and decision-making process within the game.

### 1.3. Max Replay Buffer and Target Network

In RL, a replay buffer serves as a memory repository, capturing and retaining past experiences encountered by an agent during its interactions with an environment. This data structure enables the agent to reutilize and learn from diverse historical interactions by storing state-action-reward-next_state tuples. By decoupling the immediate use of experiences and instead sampling randomnly from this stored memory during training, the replay buffer breaks the temporal correlation between consecutive experiences, leading to more stable and efficient learning. Meanwhile, a target network, often employed in algorithms like Deep-Q-Networks (DQN), functions as a stabilized reference for target Q-values during training. This secondary neural network provides less frequently updated Q-value targets, addressing the issue of rapidly changing targets and enhancing training stability by decoupling the estimation of target Q-values from the primary network's parameters. 

```python
from collections import deque
import random

MAX_MEMORY = 100_000
BATCH_SIZE = 1000

class QLearningAgent:
    def __init__(self):

        # Initialize replay buffer
        self.memory = deque(maxlen=MAX_MEMORY)

    def remember(self, state, action, reward, next_state, done):
        # Store experiences in memory
        self.memory.append((state, action, reward, next_state, done))

    def train_long_memory(self):
        # Perform training using experiences from the replay buffer
        if len(self.memory) < BATCH_SIZE:
            return

        mini_batch = random.sample(self.memory, BATCH_SIZE)
        states, actions, rewards, next_states, dones = zip(*mini_batch)
        self.trainer.train_step(states, actions, rewards, next_states, dones)
```

The applied methodology enhances stability by mitigating overfitting to recent experiences and improves learning efficiency by allowing the agent to reuse and learn from its past interactions, contributing to more stable and effective training in RL algorithms. In the `agent.py`, particularly in the `train_and_record` function, the integration of the replay buffer involves augmenting the agent's interactions with the environment to store experiences and utilizing those experiences for training. As the agent interacts with the environment in each game step, the `remember` function within the `QLearningAgent` class captures the state-action-reward-next_state tuples and stores them in the replay buffer. They are essential for off-policy learning, and the `train_short_memory` and `train_long_memory` functions facilitate short-term and long-term learning, respectively. After each completed game, the agent leverages the stored experiences by calling `train_long_memory`, which samples a batch of experiences from the replay buffer and uses these experiences to update the agent's model via the `train_step` method in the `QTrainer` class. This integration facilitates learning from a diverse set of past interactions, contributing to more stable and efficient training by breaking temporal correlations between consecutive experiences. Adjusting memory replay features in the likes of `MAX_MEMORY` and `BATCH_SIZE` in addition to initialization variables allows not only for fine-tuning of the replay buffer's capacity and the size of experiences utilized for training, but also for studying the whole agent's learning process which can be studied using commonly known evolutionary algorithms such as: Genetic Algorithms (GAs).


## 2 Standard RL

The **Standard RL** mode is the baseline Deep Q-Learning pipeline: no evolutionary tuning, no handcrafted global planner, only the DQN agent learning from environment interaction plus replay-buffer updates. In this repository, `train_standard_rl()` calls `train_RL()` in [`agent_RL.py`](https://github.com/roaked/snake-q-learning-genetic-algorithm/blob/main/agent_RL.py).

### 2.1. Learning signal and data flow

At every frame, the agent builds a compact state vector (danger flags, direction, food relation), chooses one action with epsilon-greedy logic, receives reward feedback from `play_step`, and immediately trains on that transition (`short memory`). The same transition is also stored in replay memory, so episode-end updates can train on a broader historical sample (`long memory`).

This gives two complementary update scales:

- **Local adaptation** from the newest transition (fast response).
- **Global stabilization** from replay sampling (less temporal bias).

### 2.2. Code snippet (core baseline step)

```python
state_old = agent.get_state(game)
final_move = agent.get_action(state_old)

reward, done, score, collisions, steps = game.play_step(final_move)
state_new = agent.get_state(game)

agent.train_short_memory(state_old, final_move, reward, state_new, done)
agent.remember(state_old, final_move, reward, state_new, done)
```

### 2.3. Episode boundary update

When an episode ends, the game resets and replay-based optimization is triggered:

```python
if done:
    game._init_game()
    agent.n_games += 1
    agent.train_long_memory()
```

This is the reference mode for all comparisons: if an advanced method does not beat this baseline in score trend, stability, or sample efficiency, it is not adding practical value.

```bash
python agent_RL.py rl
```

## 3 GA-Optimized RL

The **GA-Optimized RL** mode combines DQN with a **Genetic Algorithm (GA)** that tunes training hyperparameters over time. In this repository, `train_ga_optimized()` calls `train()`, which runs DQN rollouts and periodically invokes `genetic.genetic(...)` to search stronger parameter settings.

- [Risto Miikkulainen and Lex Fridman discussing the importance of neuroevolution](https://youtu.be/CY_LEa9xQtg?t=2467) in deep networks: for instance, how applying evolutionary computation is helpful in assessing architecture topology or the layer depth

### 3.1. Why evolve hyperparameters during training

A static hyperparameter set can be suboptimal across all phases of learning:

- Early phase usually benefits from more exploration.
- Mid phase benefits from more stable optimization.
- Later phase often requires stronger exploitation and less noise.

GA addresses this by searching parameter candidates against real gameplay outcomes instead of relying on one fixed manual configuration.

### 3.2. Metrics and optimization loop

Candidate sets are evaluated using gameplay metrics such as:

- `score`
- `record`
- `steps`
- `collisions`
- `same_positions`

Then selection/crossover/mutation generate the next candidate population.

```python
_, best_params, _ = genetic.genetic(
    NUM_GENERATIONS,
    score=score,
    record=record,
    steps=steps,
    collisions=collisions,
    same_positions_counter=same_positions_counter,
    game_metrics_list=game_metrics_list
)
```

### 3.3. Runtime integration with DQN

The RL policy still trains with gradient updates, but the GA periodically adjusts optimization-relevant settings:

```python
if isinstance(best_params, dict):
    agent.update_hyperparameters(best_params)
```

This creates a hybrid system:

- **DQN** learns action values.
- **GA** searches a better training regime for DQN itself.

### 3.4. "Is RL still learning if it is not winning?"

Yes. In this codebase, learning updates happen even during losing episodes:

- Per-step update: `train_short_memory(...)` runs every transition.
- Replay update: `train_long_memory()` runs at episode end.
- Negative outcomes (collisions/death) are still training signals, not ignored data.

So a GA-RL snake can lose many rounds and still update its Q-function. Losing does **not** mean "no learning"; it usually means "learning is unstable, too slow, or misaligned with the objective".

### 3.5. Why GA-RL can still fail often

In adversarial or multi-agent-like settings (including same-board battle), the environment is effectively non-stationary: opponent behavior changes the transition distribution over time. This can degrade DQN stability even when updates are happening.

Common failure modes:

1. **Noisy GA fitness signal**: short evaluation windows overfit to luck-heavy episodes.
2. **Hyperparameter drift**: frequent GA switching changes optimizer dynamics before DQN can consolidate.
3. **Objective mismatch**: pure score-based fitness may underweight survival/win consistency.
4. **Opponent pressure**: policy quality needed to survive is higher than solo-food optimization.

### 3.6. What was improved in this repository

To reduce stagnation, the GA loop now uses:

- candidate-specific fitness tracking (instead of mismatched population-level reuse),
- multi-game evaluation per candidate (`games_per_candidate=3`),
- running-average fitness updates for smoother selection pressure,
- trend-aware fitness bonus from recent score improvement,
- elite retention + crossover + range-aware mutation,
- rotating candidate schedule with periodic fallback to best-known parameters.

These reduce collapse frequency, but they do not guarantee immediate win-rate dominance in battle mode; the setting is still hard and non-stationary.

### 3.7. Practical interpretation of results

When analyzing GA-RL runs, do not use only "wins". Track:

- moving average score,
- collision/death rate over windows,
- win rate over windows,
- stability across seeds/runs.

If win-rate is flat but score trend and survival time improve, the agent is usually still learning useful behavior.

```bash
python agent_RL.py ga
```

## 4 Hamiltonian

The **Hamiltonian** mode is a deterministic controller: it does not train a neural network and does not estimate Q-values. Instead, it follows a precomputed cycle that traverses the board safely. In code, `train_hamiltonian_cycle()` calls `train_hamiltonian()`.

### 4.1. Algorithm intuition

A Hamiltonian cycle visits each cell exactly once and returns to the start. If the snake follows this cycle consistently, it avoids the classic self-trap patterns that often end RL runs during long-body phases.

### 4.2. Cycle-driven action generation

The implementation creates neighbor lookups from the cycle and maps target cells to legal turn actions:

```python
cycle = _build_hamiltonian_cycle(game.width, game.height, block_size)
next_lookup = {cycle[i]: cycle[(i + 1) % len(cycle)] for i in range(len(cycle))}
prev_lookup = {cycle[(i + 1) % len(cycle)]: cycle[i] for i in range(len(cycle))}

head = game.snake[0]
next_candidates = [next_lookup.get(head), prev_lookup.get(head)]
```

Then each candidate is converted into the action encoding used by `play_step`:

```python
for candidate in next_candidates:
    if candidate is None:
        continue
    wanted_direction = _target_direction(head, candidate)
    action = _direction_to_action(game.direction, wanted_direction)
    if action is not None:
        final_move = action
        break
```

### 4.3. Role in evaluation

Hamiltonian is valuable as a **robustness baseline**:

- no stochastic training variance,
- deterministic and reproducible,
- strong survival behavior on large-body states.

Its trade-off is lower tactical flexibility; it may be slower than a strong RL policy at short-horizon food capture because it prioritizes cycle consistency over greedy local moves.

```bash
python agent_RL.py hamiltonian
```

