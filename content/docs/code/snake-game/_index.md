---
title: "Snake Game: Genetic RL-DQN"
weight: 8
---

# Genetic Tuning for Smarter Deep Q-Learning

![213d](https://miro.medium.com/v2/resize:fit:2800/1*zRZ46MeFZMd5F52CHM6EYA.png)

# Currently working on this (some functions are already outdated)

{{< hint note>}} 
To do:
- Script for recording / saving game states
- Finish adapting the fitness function of my genetic algorithm 
- Optimize game code for game environment
{{< /hint >}}

## 1. Snake Game

The Snake game has served as a fundamental project for programming novices due to its simplicity and versatility. In this work, Pygame is used, a Python library designed for game development, to create a Snake game environment. The core motivation is to provide a controlled and adaptable setting for AI development and reinforcement learning combined with metaheuristics.

## 2. import pygame / User Controlled Snake Game 

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

In addition, SnakeGameAI Class is defined which encapsulates the game logic, managing the game state, snake movement, collision detection, and food placement.

{{< hint important >}}
Vital functions encompass:

- ```def __init__``` -- Initializing game window and start up the game state
- ```def _init_game``` -- Initialize game state variables (snake position, score, food)
- ```def _place_food``` -- Places food randomly within the game window, avoiding snake collision
- ```def _move``` -- Handling snake movement based on user or AI action
- ```def play_step ```-- Process each step of the game based on user (and later AI actions). (in addition, collects user input, move snake, check collision, update score, etc.)
{{< /hint >}}

Given ```__init__``` and ```__init__game``` functions, the snake is initialized with a starting position, consisting of three body parts (self.head and two segments) positioned horizontally to the right. The game also tracks the score, food, and frame iteration (for managing game duration).

```python
def _init_game(self):
        # Initializing game state variables (snake position, score, food)
        self.direction = Direction.RIGHT
        self.head = Point(self.width / 2, self.height / 2)
        self.snake = [
            self.head,
            Point(self.head.x - BLOCK_SIZE, self.head.y),
            Point(self.head.x - (2 * BLOCK_SIZE), self.head.y)
        ]
        self.score = 0
        self.food = None
        self.frame_iteration = 0
        self._place_food()
```

After snake initialization, the ```_move``` function adjusts the snake's direction based on the received action (from user or AI). It updates the snake's position accordingly, controlling its movement either left, right, up, or down.

```python
 def _move(self, action):
        # [straight, right, left]

        clock_wise = [Direction.RIGHT, Direction.DOWN, Direction.LEFT, Direction.UP]
        idx = clock_wise.index(self.direction)

        if np.array_equal(action, [1, 0, 0]):
            new_dir = clock_wise[idx] # No change
        elif np.array_equal(action, [0, 1, 0]):
            next_idx = (idx + 1) % 4
            new_dir = clock_wise[next_idx] # right turn r -> d -> l -> u
        else: # [0, 0, 1]
            next_idx = (idx - 1) % 4
            new_dir = clock_wise[next_idx] # left turn r -> u -> l -> d

        self.direction = new_dir

        x = self.head.x
        y = self.head.y
        if self.direction == Direction.RIGHT:
            x += BLOCK_SIZE
        elif self.direction == Direction.LEFT:
            x -= BLOCK_SIZE
        elif self.direction == Direction.DOWN:
            y += BLOCK_SIZE
        elif self.direction == Direction.UP:
            y -= BLOCK_SIZE

        self.head = Point(x, y)
```

Afterwards, the `_place_food` function randomnly places food within the game window. It ensures the food doesn't spawn on the snake's body by repositioning it until a valid location is found.

```python
def _place_food(self):
        x = random.randint(0, (self.width-BLOCK_SIZE )//BLOCK_SIZE )*BLOCK_SIZE
        y = random.randint(0, (self.height-BLOCK_SIZE )//BLOCK_SIZE )*BLOCK_SIZE
        self.food = Point(x, y)
        if self.food in self.snake:
            self._place_food()
```

In a similar manner, the `is_collision` function checks for collisions with the game window boundaries or the snake's body. If the snake hits the window boundaries or collides with itself, the game ends.

```python
def is_collision(self, pt=None):
        if pt is None:
            pt = self.head
        # hits boundary
        if pt.x > self.width - BLOCK_SIZE or pt.x < 0 or pt.y > self.height - BLOCK_SIZE or pt.y < 0: 
            return True
        # hits itself
        if pt in self.snake[1:]: 
            return True
        return False
```


Lastly, all previous vital functions are combined through the core function ```play_step``` which manages the core game loop, processing each step by collecting user input, moving the snake, and updating the game state based on collisions and food consumption & spawning. The `_update_ui` function refreshes the game display, showing the snake, food, score, and any other visual elements.

```python
def play_step(self, action):
        self.frame_iteration += 1
        # 1. input data from user
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
        
        # 2. move
        self._move(action) # update the head
        self.snake.insert(0, self.head)
        
        # 3. do we reset? / game over
        reward = 0
        game_over = False
        if self.is_collision() or self.frame_iteration > 100*len(self.snake):
            game_over = True
            reward = -10
            return reward, game_over, self.score

        # 4. place food if eaten, else keep moving
        if self.head == self.food:
            self.score += 1
            reward = 10
            self._place_food()
        else:
            self.snake.pop()
        
        # 5. update ui //
        self._update_ui()
        self.clock.tick(SPEED)
        # 6. return score or game over
        return reward, game_over, self.score
```

{{< hint warning>}}
The game itself is still under development and some of the functions might have been changed already, but not updated here. Nevertheless, current tasks to further enhance the game environment and codebase consist of:

- Complete User Input Handling: Implement the get_user_input method to capture user actions, enabling manual control of the snake.
- Display Rendering: Develop the _update_display method to visually represent the game state using Pygame's rendering capabilities.
- Enhancements: Explore additional features such as different game difficulty levels, multiplayer functionalities, or advanced AI algorithms to enrich the gaming experience.
{{< /hint>}}

## 3. Reinforcement Deep Q-Network Model