---
title: "Snake Game: Genetic RL-DQN"
weight: 8
---

# **Genetic Tuning for Smarter Deep Q-Learning**

![213d](https://miro.medium.com/v2/resize:fit:2800/1*zRZ46MeFZMd5F52CHM6EYA.png)

{{< hint warning>}} 
- I am currently writing the result details about the Genetic Algorithm
- [Risto Miikkulainen and Lex Fridman discussing the importance of neuroevolution](https://youtu.be/CY_LEa9xQtg?t=2467) in deep networks: for instance, how applying evolutionary computation is helpful in assessing architecture topology or the layer depth
- **Bug**: Parameter ranges is mutating with values from a different param_range
- **Disclaimer** - used the [freeCodeCamp.org](https://www.freecodecamp.org/news/train-an-ai-to-play-a-snake-game-using-python/) base code for the Snake Game, with a couple tweaks for my RL / Genetic Algorithm implementation
{{< /hint >}}

{{< columns >}}

![123](https://s5.gifyu.com/images/SiDzT.gif)
<--->
![123019](https://s5.gifyu.com/images/SiDzw.gif)
{{< /columns >}}


## 1. Snake Game

The Snake game has served as a fundamental project for programming novices due to its simplicity and versatility. In this work, Pygame is used, a Python library designed for game development, to create a Snake game environment. The core motivation is to provide a controlled and adaptable setting for AI development and Reinforcement Learning (RL) combined with metaheuristics or evolutionary computation.

## 2. User and AI Controlled Snake 

{{< hint important>}}
Most of the code for the game implementation was adapted using Pygame for providing a robust framework for game development in Python.
{{< /hint >}}

The code begins with initializing Pygame and setting up essential game parameters such as window size, colors, and game speed. Pygame's functionalities are leveraged for window creation, event handling, and display rendering.

```python
import random, pygame, sys, time

# Pygame initialization and window setup
check_errors = pygame.init()
if check_errors[1] > 0:
    print(f'[!] Had {check_errors[1]} errors when initializing game, exiting...')
    sys.exit(-1)
else:
    print('[+] Game successfully initialized')

# Defined other variables here, adapt fontsize, window size, colors, speed, 
# block size, etc...
```

In addition, `SnakeGameUser` and `SnakeGameAI` classes are defined which encapsulate the game logic, managing the game state, snake movement, collision detection, and food placement.

{{< hint important >}}
Vital functions encompass:

- ```def __init__``` -- Initializing game window and start up the game state
- ```def _init_game``` -- Initialize game state variables (snake position, score, food)
- ```def _place_food``` -- Places food randomly within the game window, avoiding snake collision
- ```def _move``` -- Handling snake movement based on user or AI action
- ```def play_step ```-- Process each step of the game based on user (and later AI actions). (in addition, collects user input, move snake, check collision, update score, etc.)
{{< /hint >}}

Given ```__init__``` and ```__init__game``` functions, the snake is initialized with a starting position, consisting of three body parts (`self.head` and two segments) positioned horizontally to the right - this is normally executed in good practice for developing a snake game, including representing the `self.head` at the center of the window. This setup essentially creates an initial length for the snake, allowing it to start the game with a visible length and direction. The snake initially consists of these three segments, and as the game progresses and the snake moves, additional segments will be added or removed based on its movement, food consumption, and collision detection. 

{{< hint tip >}}Having an initial length provides a visible presence of the snake on the screen, allowing the player or AI agent to see its position and direction from the beginning of the game.
{{< /hint >}}

```python
def _init_game(self):
    # Game state variables (snake position, score, food)
    start_x = self.width // 2
    start_y = self.height // 2

    self.direction = Direction.RIGHT
    self.head = Point(start_x, start_y)
    self.snake = [self.head]

    # Create initial instance of the snake with three blocks
    for i in range(1, 3):
        self.snake.append(Point(self.head.x - i * BLOCK_SIZE, start_y))

    self.score = 0
    self.food = None
    self.frame_iteration = 0
    self._place_food()
```

After snake initialization, the ```_move``` function adjusts the snake's direction based on the received action (from user or AI). This is done using a list `clockwise_directions` that represents the directional movement of the snake - right, down, left, up (clockwise). Moreover, it determines the intended change in direction: no change, right turn, or left turn. Calculating the new direction index by locating the current direction within the `clockwise_directions` list and adjusting it based on the determined change, it then updates the snake's direction accordingly. 

{{< hint note >}}
Case scenarios:
If the action indicates no change [1, 0, 0] -> snake maintains its current direction.
If the action indicates a right turn [0, 1, 0] -> snake updates the direction to the next index in the clockwise order.
If the action indicates a left turn [0, 0, 1] -> snake updates the direction to the previous index in the clockwise order (effectively, anti-clockwise).
{{< /hint >}}

Furthermore, using a dictionary `movement_adjustments` that maps directions for movement adjustments (changes in `x` and `y` coordinates), it applies the corresponding adjustment to the snake's head position, effectively moving it in the intended direction by a distance specified by the `BLOCK_SIZE`, aligning its position with the game grid.

```python
 def _move(self, action):
        # Define clockwise directions
        clockwise_directions = [Direction.RIGHT, Direction.DOWN,
                                 Direction.LEFT, Direction.UP]
        current_direction = self.direction

        # Define action codes for each movement direction
        no_change_action = [1, 0, 0]
        right_turn_action = [0, 1, 0]
        left_turn_action = [0, 0, 1]

        # Determine the change in direction based on action
        if action == no_change_action:
            direction_change = 0
        elif action == right_turn_action:
            direction_change = 1
        elif action == left_turn_action:
            direction_change = -1

        # Calculate the new direction index
        current_index = clockwise_directions.index(current_direction)
        new_index = (current_index + direction_change) % 4
        new_direction = clockwise_directions[new_index]

        # Update the direction
        self.direction = new_direction

        # Define movement adjustments for each direction
        movement_adjustments = {
            Direction.RIGHT: (BLOCK_SIZE, 0),
            Direction.LEFT: (-BLOCK_SIZE, 0),
            Direction.DOWN: (0, BLOCK_SIZE),
            Direction.UP: (0, -BLOCK_SIZE)
        }

        # Apply movement adjustments to update the head position
        move_x, move_y = movement_adjustments[self.direction]
        self.head = Point(self.head.x + move_x, self.head.y + move_y)
```

Afterwards, the `_place_food` selects a random position within the game grid to place the food for the snake. Initially creating a set containing all current positions occupied by the snake segments, it enters a loop that generates random `x` and `y` coordinates within the game boundaries, scaled by the block size, effectively aligning with the grid. These coordinates form a potential new food position. The loop continues generating random positions until it finds a position that doesn't coincide with any segment of the snake. Once a suitable position is found, it assigns this new position as the food's location, effectively placing the food at an unoccupied spot within the game grid, ensuring it does not overlap with the snake's current positions.

```python
def _place_food(self):
    snake_positions = {point for point in self.snake}  # set of snake positions

    # Generate random positions until an available one is found
    while True:
        x = random.randint(0, (self.width - BLOCK_SIZE) // BLOCK_SIZE) * BLOCK_SIZE
        y = random.randint(0, (self.height - BLOCK_SIZE) // BLOCK_SIZE) * BLOCK_SIZE

        new_food_position = Point(x, y)
        if new_food_position not in snake_positions:
            self.food = new_food_position
            break
```

For defining the game over condition, the `is_collision` function determines if a collision occurs at a given point within the game. Initially checking the point's existence, defaulting to the snake's head if not provided, it then examines whether the point lies beyond the game's boundaries, assessing if its coordinates exceed the game area's width or height. Additionally, it verifies if the point coincides with any part of the snake's body, excluding the head. If the point is outside the game boundaries or matches a segment of the snake's body, the function returns `True`, indicating a collision has occurred. Otherwise, it returns `False`, signifying no collision at that specific point in the game.

```python
def is_collision(self, pt=None):
        if pt is None:
            pt = self.head

        # Boundary collision check
        collides_with_boundary = (
            pt.x > self.width - BLOCK_SIZE or
            pt.x < 0 or
            pt.y > self.height - BLOCK_SIZE or
            pt.y < 0
        )

        # Snake body collision check
        collides_with_snake = pt in self.snake[1:]
        
        return collides_with_boundary or collides_with_snake
```

Lastly, the `play_step` function manages a single step within the game loop. It increments the frame count to track game progress and handles events like quitting the game. It updates the snake's movement based on the received action, adding a new head position to the snake's body. In the case of a user-controlled snake game, it must take as input, the respective key that is being pressed at the time and adjust the action based on that specific task. Otherwise, `play_step` will take the action as an input value (in case of AI controlled).

```python
def play_step(self):
        self.frame_iteration +=1

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    self.direction = Direction.LEFT
                elif event.key == pygame.K_RIGHT:
                    self.direction = Direction.RIGHT
                elif event.key == pygame.K_UP:
                    self.direction = Direction.UP
                elif event.key == pygame.K_DOWN:
                    self.direction = Direction.DOWN
```

Moreover, the function checks for game-ending conditions — such as collision with itself or exceeding a frame limit—and sets the game over status accordingly, applying penalties if necessary. If the snake consumes the food, it increments the score and updates the food position. After these actions, it refreshes the game display and controls the game's frame rate before returning the reward earned, the game over status, and the current score, providing essential information for the game loop to proceed.

```python
def play_step(self, action):
    self.frame_iteration += 1

    for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
    
    # Move the snake
    self._move(action)
    self.snake.insert(0, self.head)
    
    # Check for game over conditions
    game_over = False
    reward = 0
    collision_or_frame_limit = self.is_collision() or
            self.frame_iteration > 100 * len(self.snake)

    if collision_or_frame_limit:
        game_over = True
        reward = -10
        return reward, game_over, self.score
    
    # Check if the snake has eaten the food
    if self.head == self.food:
        self.score += 1
        reward = 10
        self._place_food()
    else:
        self.snake.pop()
    
    # Update UI and clock
    self._update_ui()
    self.clock.tick(SPEED)
    
    # Return game status and score
    return reward, game_over, self.score
```

![123](https://s5.gifyu.com/images/SiDzT.gif)


## 3. Reinforcement Deep Q-Network Model

### 3.1. LinearQNet and QTrainer Classes

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

### 3.2. Deploying a Reinforcement Learning Agent

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

### 3.3. Max Replay Buffer and Target Network

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


## 4. Genetic Optimization of a RL Deep-Q-Network


In the context of optimizing key parameters for RL — such as batch size, learning rate, memory capacity for replay buffers, and the architecture of a target network — GAs provide a systematic approach. 

### 4.1. Parameter Space Definition

The parameter space definition refers to the specification and range of parameters that influence the architecture, behavior, and learning process of a our DQN. This includes continuous parameters like learning rate and discount factor, discrete parameters such as activation functions and optimizer types, and integer parameters like the number of hidden layers and neurons per layer.

{{< hint info >}}

**Continuous Parameters**

- `learning_rate`: Influences the speed at which the DQN learns. Higher values enable faster learning, while lower values promote stability.
- `discount_factor`: Determines the importance of future rewards in the learning process. Values closer to 1 emphasize long-term rewards.
- `dropout_rate`: Affects the number of neurons dropped out during training to prevent overfitting.
- `exploration_rate`: Controls the level of exploration versus exploitation in the learning process.

**Discrete Parameters**

- `batch_size`: Dictates the number of experiences sampled from the replay buffer for training.
- `activation_function`: Determines the type of activation function used in neural network layers (e.g., ReLU, sigmoid, tanh).
- `optimizer`: Specifies the optimization algorithm for updating the DQN's parameters during training (e.g., Adam, SGD, RMSprop).


**Integer Parameters**

- `num_hidden_layers`: Specifies the number of hidden layers in the neural network.
- `neurons_per_layer`: Defines the number of neurons in each hidden layer.

{{< /hint >}}

For each parameter, a defined range or set of possible values is established. These ranges are carefully chosen based on prior knowledge, domain expertise, or empirical observations of their impact on the DQN's behaviour and performance. By exploring this parameter space, the GA aims to discover configurations that maximize game-related metrics, such as higher scores or fewer steps.

```python
param_ranges = {
    # Continuous parameters
    # Alpha / Higher values allow faster learning, while lower values ensure more stability
    'learning_rate': (0.001, 0.1), 
    #Gamma / Closer to 1 indicate future rewards are highly important, emphasizing long-term rewards
    'discount_factor': (0.9, 0.999), 
    # Higher drops out a more neurons -> prevent overfit in complex models/datasets with limited samples
    'dropout_rate': (0.1, 0.5), 
    # Epsilon /More exploration -> Possibly better actions /Lower -> More stability using learned policy
    'exploration_rate': (0.1, 0.5), 
    
    # Discrete parameters
    # Number of experiences sampled from the replay buffer for training
    'batch_size': [10, 100, 250, 500, 1000, 2000, 5000], 
    'activation_function': ['relu', 'sigmoid', 'tanh'],
    'optimizer': ['adam', 'sgd', 'rmsprop'], 
    
    # Integer parameters (num_inputs, num_neurons, num_outputs of NN)
    'num_hidden_layers': [1, 2, 3, 4, 5],
    'neurons_per_layer': [32, 64, 128, 256, 512, 1024]
    }
```

### 4.2. Genetic Algorithm

The algorithm starts with the initialization of a class `GeneticAlgorithm` and an initial population, both comprising diverse parameter sets. This involves creating a collection of potential solutions, representing different combinations of parameters within the specified ranges. The population's diversity plays a pivotal role in enabling the exploration of a broad spectrum of parameter configurations.


```python
def generate_population(self, population_size, param_ranges, chromosome_length): 
    #Random init or heuristic init (using prior info)
    population = []
    for _ in range(population_size):
        params = {}
        for param, value_range in param_ranges.items():
            if isinstance(value_range, tuple):  # Init cont. parameters
                params[param] = random.uniform(value_range[0], value_range[1])
            elif isinstance(value_range, list):  # Discrete parameters
                params[param] = random.choice(value_range)
            elif isinstance(value_range, int):  # Integer parameters
                params[param] = random.randint(0, value_range)
            elif isinstance(value_range, str):  # String parameters
                params[param] = value_range  # Set the string value directly
        population.append(params)
    return population
```

Afterwards, the `fitness function` evaluates the performance of the DQN by considering various game-related metrics such as `score`, `record`, `steps`, `collisions`, and `same_positions_counter`. These metrics are utilized to compute a fitness score that quantifies the effectiveness of a parameter set within the DQN. Normalization of metrics like `score` and `steps` occurs next, ensuring that these metrics are on a comparable scale for fair evaluation. Normalization enables a coherent assessment where disparate metrics contribute equitably to the overall fitness score. Moreover, the function integrates conditional adjustments like penalties for certain conditions. For instance, it penalizes repeated visits to the same positions (`penalty_same_positions`) or inefficient utilization of steps (`penalty_efficiency_decay`), reflecting a meticulous consideration of nuanced gameplay elements.

```python
def fitness_function(self, score, record, steps, collisions, same_positions_counter):
    # Metrics and weights
    weight_score = 0.75
    weight_steps, MAX_POSSIBLE_STEPS = 0.25, 300

    # Normalize metrics
    normalized_score = score / record if record != 0 else 0
    normalized_steps = 1 - (steps / MAX_POSSIBLE_STEPS) if MAX_POSSIBLE_STEPS != 0 else 0

    # Penalty for revisiting same positions > 150 (5%)
    penalty_same_positions = 0.05 if same_positions_counter > 150 else 0

    # Efficiency decay (5%)
    efficiency_decay = max(0, (steps - score) / MAX_POSSIBLE_STEPS)
    penalty_efficiency_decay = 0.05 * efficiency_decay
```

Ultimately, the fitness score is computed by merging the normalized metrics, weighted according to their significance, and factoring in penalties or bonuses where applicable. The goal is to synthesize a comprehensive fitness score that encapsulates the effectiveness of a particular parameter set in improving the DQN's performance within the game environment

```python
# Calculate fitness
fitness = (
    (normalized_score * weight_score) +
    (normalized_steps * weight_steps) -
    penalty_same_positions - penalty_efficiency_decay
)

return max(0, fitness)  # Ensure non-negative fitness
```

The fitness scores for the entirity of the population of parameter sets can be computed using `calculate_population_fitness`. It ensures the inclusion of at least 5 recent game metrics for evaluation or uses all available metrics if fewer than 5 are present. By iterating through these metrics, it extracts essential indicators like `score`, `record`, `steps`, `collisions`, and `same_positions_counter` for each individual set. Afterwards, there is selection: it determines which individuals, represented as parameter sets, proceed to the next generation based on their fitness scores. Initially, it normalizes the fitness scores received from the `fitness_function`, ensuring these scores reflect the effectiveness of parameter sets in enhancing the DQN's performance. Subsequently, it computes probabilities for each individual proportional to their fitness scores, favouring individuals with higher fitness. Employing a roulette wheel selection strategy, the method then selects individuals from the population according to these probabilities, allowing higher-scoring individuals a greater chance of being chosen. This selection process forms the basis for creating a new population consisting of the chosen individuals, facilitating the iterative evolution and refinement of parameter sets across successive generations within the GA framework.

```python
def selection(self, population, fitness_scores):
    # Normalize fitness scores to probabilities
    total_fitness = sum(fitness_scores)

    if total_fitness == 0:
        probabilities = [1 / len(fitness_scores)] * len(fitness_scores)
    else:
        probabilities = [fitness / total_fitness for fitness in fitness_scores]

    # Ensure probabilities array size matches population size
    while len(probabilities) < len(population):
        probabilities.append(0.0)

    # Select based on fitness (roulette wheel selection) //
    # replace = True means one chromosome can be picked more than 1 time
    selected_indices = np.random.choice(
        len(population), 
        size=self.population_size, 
        replace=True, 
        p=probabilities / np.sum(probabilities)  
        # Normalize probabilities to sum up to 1
    )

    # Create a new population based on the selected indices
    new_population = [population[idx] for idx in selected_indices] 
    # List Comprehension - New population Array

    return new_population
```

Following the `selection` of individuals, the `crossover` function exemplifies genetic recombination between two parent individuals to produce offspring individuals as potential solutions within the GA. Initially, it ensures that both parents have the same length of genetic information, converting them into lists if they are initially dictionaries. Then, based on a randomnly determined crossover probability (`crossover_rate`), it either conducts the crossover process or maintains the parents as they are. When the crossover occurs (determined by a random probability check), it identifies a crossover point within the genetic information and generates two offspring by swapping the genetic information of the parents before and after this point. These newly created offspring individuals represent combinations of genetic material from both parents, potentially leading to diverse and potentially advantageous solutions within the population. If the crossover does not happen, it returns the original parent individuals as the output.

```python
"""Single-point crossover for two parent individuals. 
Can explore two-point crossover, uniform crossover, elitist crossover, etc."""
def crossover(self, parent1, parent2, crossover_rate):

    if isinstance(parent1, dict) and isinstance(parent2, dict):
        # Convert dictionary values to lists
        parent1 = list(parent1.values())
        parent2 = list(parent2.values())

    assert len(parent1) == len(parent2) # Only if same len

    if random.random() < crossover_rate:
        # Crossover point
        crossover_point = random.randint(1, len(parent1) - 1)

        # Create offspring by combining parent genes
        offspring1 = parent1[:crossover_point] + parent2[crossover_point:]
        offspring2 = parent2[:crossover_point] + parent1[crossover_point:]

        return offspring1, offspring2
    else: # If crossover doesn't happen, return the parents
        return parent1, parent2
```

The `mutation` function operates on individuals — comprising genetic material representing potential solutions — and introduces small alterations to their genetic makeup based on a predefined `mutation rate`. This genetic variation mechanism enables exploration of novel solution spaces. The function iterates through the genetic information of an individual and, for each gene, checks if a randomly generated probability falls below the specified `mutation rate`. If so, it attempts to modify the gene: for numeric values, it converts the gene to an integer and performs a transformation (in this case, subtracting the value from 1), showcasing the alteration; for non-numeric values, it retains the original gene. The function aggregates these modified genes, generating a mutated individual with potential genetic diversity that might lead to the exploration of new and potentially beneficial solution areas within the GA's solution space.

```python
"""According to Genetic Algorithm, after crossover (breeding), we apply mutation 
to the resulting offspring to introduce small changes to their genetic material 
depending on the mutation rate, this helps explores new areas of solution space"""

def mutation(self, individual, mutation_rate):
    mutated_individual = []
    for gene in individual:
        if random.random() < mutation_rate:
            try: # Assuming 'gene' is str to convert to a numerical
                gene = int(gene)  # Convert 'gene' to an integer
                mutated_gene = 1 - gene
            except ValueError:
                print("'gene' might not be a numeric value.")
        else:
            mutated_gene = gene
        mutated_individual.append(mutated_gene)
    return mutated_individual
```

Lastly, it extends the existing offspring list with newly generated offspring, resulting from genetic recombination and mutation processes. Subsequently, it implements an elitism strategy, identifying the top-performing individuals within the population based on their fitness scores and replacing the least fit part of the population with these elite individuals. This preserves highly fit solutions from the current population for the next generation, ensuring the retention of successful traits. Additionally, it evaluates the fitness of each parameter set in the current population, identifying the best-performing parameters by comparing their fitness against a stored best fitness value, thereby capturing the best parameters encountered during the Genetic Algorithm's execution. 

```python
# Replace the least fit part of the population with offspring
elite_count = int(self.population_size * 0.1)  # Keep top 10% as elite
elite_indices = sorted(range(len(fitness_scores)), key=lambda i: fitness_scores[i], reverse=True)[:elite_count]

for idx in elite_indices:
    offspring[idx] = self.population[idx]  # Preserve elite chromosomes
```

## 5. Results


![123019](https://s5.gifyu.com/images/SiDzw.gif)

`(to insert images)`

Maybe introduce RL vs Genetic one plot...

## 6. Outcomes 

The GA can efficiently search through a defined parameter space to identify better sets of hyperparameters for the RL model. This can lead to enhanced performance, quicker convergence, and increased stability within the learning process. By fine-tuning the hyperparameters, the RL model might exhibit improved performance metrics such as higher rewards, more efficient learning, reduced training time, and potentially better generalization to unseen data, unlike using only the RL model, where it took approximately 150 games to converge to good scores.

{{< hint important >}}
An interesting take on GA is the ability to explore diverse solution spaces, by doing so it might uncover parameter configurations that promote better exploration-exploitation trade-offs, addressing challenges like the exploration-exploitation dilemma common in RL.
{{< /hint >}}

More to add....