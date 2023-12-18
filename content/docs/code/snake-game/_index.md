---
title: "Snake Game: Genetic RL-DQN"
weight: 8
---

# **Genetic Tuning for Smarter Deep Q-Learning**

![213d](https://miro.medium.com/v2/resize:fit:2800/1*zRZ46MeFZMd5F52CHM6EYA.png)

# Working on - some functions might be outdated

{{< hint note>}} 
General Fixes:

- Script for recording / saving game states
- Finish adapting the fitness function of the genetic algorithm 
- Post results
{{< /hint >}}


{{< columns >}}

![123](https://s5.gifyu.com/images/SiDzT.gif)
<--->
![123019](https://s5.gifyu.com/images/SiDzw.gif)
{{< /columns >}}


## 1. Snake Game

The Snake game has served as a fundamental project for programming novices due to its simplicity and versatility. In this work, Pygame is used, a Python library designed for game development, to create a Snake game environment. The core motivation is to provide a controlled and adaptable setting for AI development and reinforcement learning combined with metaheuristics.

## 2. User Controlled Snake 

{{< hint important>}}
Most of the code for the game implementation was adapted using Pygame for providing a robust framework for game development in Python. -- [Pygame Documentation](pygame.org/docs)
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

# Defined other variables here, adapt fontsize, window size, colors, speed, block size, etc...
```

In addition, `SnakeGameAI` class is defined which encapsulates the game logic, managing the game state, snake movement, collision detection, and food placement.

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

After snake initialization, the ```_move``` function adjusts the snake's direction based on the received action (from user or AI). This is done using a list `clock_wise` that represents the directional movement of the snake - right, down, left, up (clockwise). It determines the current direction's index and handles changes in direction based on the received action:

{{< hint note >}}
Case scenarios:
If the action indicates no change [1, 0, 0] -> snake maintains its current direction.
If the action indicates a right turn [0, 1, 0] -> snake updates the direction to the next index in the clockwise order.
If the action indicates a left turn [0, 0, 1] -> snake updates the direction to the previous index in the clockwise order (effectively, anti-clockwise).
{{< /hint >}}

Based on the updated direction, the function calculates the new `position (x, y)` for the snake's head. It increments or decrements the `x` or `y` coordinate of the head based on the direction (right, left, down, up), moving it by `BLOCK_SIZE`, which represents the size of each snake block.

```python
 def _move(self, action):
        # Define clockwise directions
        clockwise_directions = [Direction.RIGHT, Direction.DOWN, Direction.LEFT, Direction.UP]
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

Afterwards, the `_place_food` function randomnly places food within the game window. It ensures the food doesn't spawn on the snake's body by repositioning it until a valid location is found.

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

For defining the game over condition, two possibilities can be addressed in the `is_collision` function. The first possibility checks whether the specified point (defaulted to `self.head` if no point is provided) is outside the game window. It compares the `x` and `y` coordinates of the point against the window boundaries, considering the size of the game elements (the snake blocks) to ensure they remain within the window. Additionally, the second possibility consideres if the specified point (or `self.head`) is present within the `self.snake` list beyond the first element. This effectively checks if the snake's head or a specified point coincides with any part of the snake's body excluding the head. If a collision is detected, it means the snake has collided with itself.

```python
def play_step(self, action):
    self.frame_iteration += 1
    
    # Move the snake
    self._move(action)
    self.snake.insert(0, self.head)
    
    # Check for game over conditions
    game_over = False
    reward = 0
    collision_or_frame_limit = self.is_collision() or self.frame_iteration > 100 * len(self.snake)
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

For visualization, the `_update_ui` function serves as the visual engine of the game, orchestrating the graphical elements displayed to the player. It begins by wiping the display clean with a black background, then proceeds to render the snake's body as a sequence of blue rectangles, each block outlined by a smaller blue rectangle, creating a bordered appearance. Next, it draws the red food block at its designated position within the game grid. Additionally, it generates the textual representation of the player's score, showcasing it prominently in the top-left corner of the screen. Finally, by updating the display, it ensures that all these visual elements accurately reflect the ongoing state of the game, providing players with a real-time view of the snake's movement, the position of the food, and their current score as they play.

```python
def _update_ui(self):
    self.display.fill(BLACK)

    # Draw the snake
    for pt in self.snake:
        snake_rect = pygame.Rect(pt.x, pt.y, BLOCK_SIZE, BLOCK_SIZE)
        pygame.draw.rect(self.display, BLUE1, snake_rect)
        pygame.draw.rect(self.display, BLUE2, snake_rect.inflate(-8, -8))  # Inflate the rectangle

    # Draw the food
    food_rect = pygame.Rect(self.food.x, self.food.y, BLOCK_SIZE, BLOCK_SIZE)
    pygame.draw.rect(self.display, RED, food_rect)

    # Render the score text
    score_text = font.render("Score: " + str(self.score), True, WHITE)
    self.display.blit(score_text, (0, 0))

    # Update the display
    pygame.display.flip()
```


{{< hint warning>}}
The game itself is still under development and some of the functions might have been changed already, but not updated here. Nevertheless, current tasks to further enhance the game environment and codebase consist of:

- Complete User Input Handling: Implement the get_user_input method to capture user actions, enabling manual control of the snake.
- Display Rendering: Develop the _update_display method to visually represent the game state using Pygame's rendering capabilities.
- Enhancements: Explore additional features such as different game difficulty levels, multiplayer functionalities, or advanced AI algorithms to enrich the gaming experience.
{{< /hint>}}

![123](https://s5.gifyu.com/images/SiDzT.gif)


## 3. Reinforcement Deep Q-Network Model

`(to insert theoretical background)`

The [LinearQNet](https://github.com/roaked/snake-q-learning-genetic-algorithm/blob/main/model.py) class represents a simple neural network architecture tailored for Q-value approximation in reinforcement learning. It consists of two linear layers initialized during instantiation, with the first layer transforming input features to a hidden layer and the second layer producing Q-values for different actions. 

```python
class LinearQNet(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super().__init__()
        self.linear1 = nn.Linear(input_size, hidden_size)
        self.linear2 = nn.Linear(hidden_size, output_size)
```

The forward method defines the forward pass, applying a rectified linear unit (ReLU) activation to the hidden layer's output before generating the Q-values. Additionally, the save method facilitates saving the model's state dictionary `self.state_dict()` to a specified file path using PyTorch's `torch.save` functionality, ensuring the preservation of trained model parameters.

```python
    def forward(self, x):
        x = F.relu(self.linear1(x))
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

Employing the Q-learning update rule, the function computes target Q-values based on the predicted values, updating them according to the received rewards and the next state's Q-values if the episode hasn't terminated. Subsequently, it calculates the Mean Squared Error (MSE) loss between the predicted and target Q-values and performs backpropagation to derive gradients for updating the neural network's parameters. Finally, the optimizer applies these gradients to adjust the model's weights through optimization, enhancing the network's ability to approximate accurate Q-values for efficient decision-making in reinforcement learning scenarios.

```python
    def train_step(self, state, action, reward, next_state, done):
        state = torch.tensor(state, dtype=torch.float)
        next_state = torch.tensor(next_state, dtype=torch.float)
        action = torch.tensor(action, dtype=torch.long)
        reward = torch.tensor(reward, dtype=torch.float)

        pred = self.model(state)
        target = pred.clone()

        # Q-learning update rule

        if len(state.shape) == 1: # (1,x)
            state = torch.unsqueeze(state, 0)
            next_state = torch.unsqueeze(next_state, 0)
            action = torch.unsqueeze(action, 0)
            reward = torch.unsqueeze(reward, 0)
            done = (done, )

        # Predicting Q-values based on current state-action pair

        pred = self.model(state)
        target = pred.clone()
        for idx in range(len(done)):
            Q_new = reward[idx]
            if not done[idx]:
                Q_new = reward[idx] + self.gamma * torch.max(self.model(next_state[idx]))

            target[idx][torch.argmax(action[idx]).item()] = Q_new

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

## 4. Genetic Tuning of a RL Deep-Q-Network

`(to insert theoretical background)`

![123019](https://s5.gifyu.com/images/SiDzw.gif)

## 5. Results

`(to insert images)`

## 6. Outcomes 