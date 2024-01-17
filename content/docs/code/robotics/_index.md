---
title: "Robotics: Kin, Dynamics & Control"
weight: 7
---

## KUKA Robotics: From Kinematics to Motion Planning

[To add report when I have time :)](https://github.com/roaked/robotics-kinematics-dynamics-and-control)

![soakd](https://blogs.mathworks.com/images/seth/2009Q2/Robot.gif)

{{< hint important >}}

- I would at some point like to implement a safe reinforcement learning whilst heading for optimistic exploration within a large-scale manufacturing setting instead of defining a specific trajectory! 

I think it would turn out a very informative and interesting project!
{{< /hint >}}


### 1. Abstract

This research centers on the kinematic analysis of the KUKA KR6 R700 robot, which is installed on a linear KL100 axis. The KUKA KR6 R700 is an industrial robot designed for tasks such as tool and fixture handling, as well as processing or transferring components or products.

The initial phase of studying the robot involves analyzing and constructing its kinematic model based on the Denavit-Hartenberg convention, with a focus on selecting the link reference frames. 

Presented and validated through various Simulink models, it is intended to ease the extraction of the robot's direct kinematics, inverse kinematics, geometric Jacobian, and Closed Loop Inverse Kinematics (CLIK).

### 2. Kinematics

The robot consists on the presence of 6 revolute joints. Additionally, there is a single prismatic joint that facilitates horizontal movement of the robot. Consequently, the overall configuration of the robot comprises a total of 7 links.

![1](https://live.staticflickr.com/65535/53469950810_c67521134d.jpg)

![2](https://live.staticflickr.com/65535/53469851949_b3914f719c.jpg)

Utilizing the convention of [Denavit-Hartenberg (DH)](https://en.wikipedia.org/wiki/Denavit%E2%80%93Hartenberg_parameters), reference frames for each link can be drawn.

![3](https://live.staticflickr.com/65535/53469851939_221cf7f814.jpg)

| Link | \(d_i\) | \(\theta_i\) | \(a_i\) | \(\alpha_i\) | \( \text{offset} \) |
|------|---------|--------------|--------|--------------|---------------------|
| 1    | \(d_1\) | 0            | 0      | \(-\frac{\pi}{2}\) | 0                   |
| 2    | 0       | \(\theta_2\) | 0.025  | \(\frac{\pi}{2}\)  | 0                   |
| 3    | 0       | \(\theta_3\) | 0.315  | 0                | 0                   |
| 4    | 0       | \(\theta_4\) | 0.035  | \(\frac{\pi}{2}\)  | 0                   |
| 5    | -0.365  | \(\theta_5\) | 0      | \(-\frac{\pi}{2}\) | 0                   |
| 6    | 0       | \(\theta_6\) | 0      | \(\frac{\pi}{2}\)  | 0                   |
| 7    | -0.080  | \(\theta_7\) | 0      | \(\pi\)           | 0                   |


