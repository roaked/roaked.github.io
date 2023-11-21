---
title: RedBull Drone Racing
weight: 1
---

# RedBull Racing

## 1.1 RedBull Racing

The world of Red Bull drone racing stands at the intersection of high-speed competition and very advanced technology. In this exhilarating arena, unmanned aerial vehicles (UAVs) navigate intricate courses filled with obstacles, pushing the limits of flight capabilities and piloting skills. To revolutionize this dynamic sport, our project aims to integrate advanced AI, computer vision, and simulated dynamics to elevate drone performance and safety within the competitive realm.

## 1.2 Overview

This interesting concept revolves around the development of a comprehensive system that seamlessly combines state-of-the-art technologies to enhance Red Bull drone racing. It is desired to fuse AI-driven algorithms, computer vision systems, and simulated dynamics modeling to optimize drone navigation through complex racecourses.

The branches in which we are hoping to develop are:

- **Computer Vision or AI-Enabled Obstacle Detection and Avoidance**: Implement sophisticated AI algorithms coupled with computer vision systems onboard the drones. These systems will enable real-time obstacle detection and swift maneuvering to avoid collisions during high-speed races.

- **Simulated Dynamics Modeling**: Develop intricate models that encapsulate the intricate dynamics of the drones, including aerodynamics, motor responses, and environmental interactions. This simulation will accurately predict drone behavior in various scenarios, aiding in performance optimization.

- **Simulink Integration via TCP/IP**: Establish a robust connection between the simulated drone dynamics model in Simulink and the actual drones using TCP/IP. This connection facilitates real-time communication, enabling testing and refinement of control algorithms in a virtual environment. This will be done by using an external library.

This approach enhances drone maneuverability and agility when navigating intricate racecourses, elevates safety by detecting and avoiding obstacles in real time, and fine-tunes control algorithms for high-speed racing situations.

## 1.2 System Dynamics Modeling 

Breaking down the drone into subsystems helps manage its complexity. The quadrotor has three main parts:

- Actuation System: This handles the rotors' model, the forces they generate (Fp), and the moments they create (Mp). Each rotor gets an input voltage (Vmi) and produces an angular velocity ({{< katex display >}}\omega{{< /katex >}} i).

- Movement System: This part deals with forces (not just from rotors but also external factors like wind) and moments that drive the drone's movement. It uses physics laws, dynamics, and kinematics to determine the drone's position and velocity.

- Instrumentation System: This part involves the sensor model. In this project phase, the sensors are simplified as ideal sensors, meaning they have a gain of one.

Breaking it down this way helps in focusing on and understanding each aspect separately, making the overall understanding and development of the drone more manageable.

### 1.2.1 Actuation Subsystem

The actuation subsystem involves individual DC motors powering propellers to generate lift forces in the drone's four independently controlled motors (i=1,2,3,4). The values of I and {{< katex display >}}\omega{{< /katex >}} change over time based on the applied motor voltage, Vm, while constants like Lm, Rm, Ke, Kt, and Jm characterize the motor. This results in a second-order system for each drone motor, expressed as:

{{< katex display >}}
\dot{\tilde{x}} &= A\tilde{x} + B\tilde{u} = \begin{bmatrix} \dot{\tilde{I}}_i \\ \dot{\tilde{\Omega}} \end{bmatrix} = \begin{bmatrix} a_{11} & a_{12} \\ a_{21} & a_{22} \end{bmatrix} \begin{bmatrix} \tilde{I}_i \\ \Omega \end{bmatrix} + \begin{bmatrix} b_2 \\ 0 \end{bmatrix} \tilde{V}_m \\
\tilde{y} &= C\tilde{x} + D\tilde{u} = \Omega, \quad i = [0 \, 1]
\end{align*} \]
{{< /katex >}}

The parameters in matrices A and B are determined by the motor specifications and the chosen flight conditions for linearization.