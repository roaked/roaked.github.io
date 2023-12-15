---
title: "Snake Game: Genetic RL-DQN"
weight: 8
---

# Genetic Tuning for Smarter Deep Q-Learning

![213d](https://miro.medium.com/v2/resize:fit:2800/1*zRZ46MeFZMd5F52CHM6EYA.png)

(*Currently developing...*)

## 1. Snake Game

The Snake game has served as a fundamental project for programming novices due to its simplicity and versatility. In this work, Pygame is used, a Python library designed for game development, to create a Snake game environment. The core motivation is to provide a controlled and adaptable setting for AI development and reinforcement learning combined with metaheuristics.

## 2. import pygame

The code begins with initializing Pygame and setting up essential game parameters such as window size, colors, and game speed. Pygame's functionalities are leveraged for window creation, event handling, and display rendering.

import random, pygame, sys, time

```python
# Pygame initialization and window setup
check_errors = pygame.init()
if check_errors[1] > 0:
    print(f'[!] Had {check_errors[1]} errors when initializing game, exiting...')
    sys.exit(-1)
else:
    print('[+] Game successfully initialized')

# Defined other variables here, adapt fontsize, window size, colors, speed, block size, 
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

Given ```def__init__``` and ```def __init__game``` the snake is initialized with a starting position, consisting of three body parts (self.head and two segments) positioned horizontally to the right. The game also tracks the score, food, and frame iteration (for managing game duration).

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

After snake initialization, the ```def _move``` adjusts the snake's direction based on the received action (from user or AI). It updates the snake's position accordingly, controlling its movement either left, right, up, or down.


The previous vital functions can be thoroughly explored: The ```play_step``` function manages the core game loop, processing each step by collecting user input, moving the snake, and updating the game state based on collisions and food consumption. The ```get_user_input``` function is designed to retrieve user actions, ```while _update_display``` is intended to handle visual updates.



{{< hint tip>}}
Future Directions to further enhance the game environment and codebase:

- Complete User Input Handling: Implement the get_user_input method to capture user actions, enabling manual control of the snake.
- Display Rendering: Develop the _update_display method to visually represent the game state using Pygame's rendering capabilities.
- Enhancements: Explore additional features such as different game difficulty levels, multiplayer functionalities, or advanced AI algorithms to enrich the gaming experience. (`currently under development`)
{{< /hint>}}