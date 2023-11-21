---
title: RedBull Racing Competition
weight: 1
---

# **An Autonomous Drone Racing Competition**

## 1 RedBull Racing 

The world of Red Bull drone racing stands at the intersection of high-speed competition and very advanced technology. In this exhilarating arena, unmanned aerial vehicles (UAVs) navigate intricate courses filled with obstacles, pushing the limits of flight capabilities and piloting skills. To revolutionize this dynamic sport, I developped a model that aims to integrate advanced AI, computer vision, and simulated dynamics to elevate drone performance and safety within the competitive realm.

## 2 Overview

This interesting concept revolves around the development of a comprehensive system that seamlessly combines state-of-the-art technologies to enhance Red Bull drone racing. It is desired to fuse AI-driven algorithms, computer vision systems, and simulated dynamics modeling to optimize drone navigation through complex racecourses.

The following topics are studied:

- **Computer Vision or AI-Enabled Obstacle Detection and Avoidance**: Implement sophisticated AI algorithms coupled with computer vision systems onboard the drones. These systems will enable real-time obstacle detection and swift maneuvering to avoid collisions during high-speed races.

- **Simulated Dynamics Modeling**: Develop intricate models that encapsulate the intricate dynamics of the drones, including aerodynamics, motor responses, and environmental interactions. This simulation will accurately predict drone behaviour in various scenarios, aiding in performance optimization.

- **Simulink Integration via TCP/IP**: Establish a robust connection between the simulated drone dynamics model in Simulink and the actual drones using TCP/IP. This connection facilitates real-time communication, enabling testing and refinement of control algorithms in a virtual environment. This will be done by accessing an external library.

As previously mentioned, this approach enhances drone maneuverability and agility when navigating intricate racecourses, elevates safety by detecting and avoiding obstacles in real time, and fine-tunes control algorithms for high-speed racing situations.

## 3 Work Division

Given how complex this project turned out to be, I have decided to structure it into distinct sections to comprehensively cover various aspects of the system design:

- **Drone System Dynamics Modeling and Controller Design**: This section will focus on developing and analyzing models for the drone's dynamics. It will delve into understanding how the drone behaves under different conditions and designing an appropriate controller to optimize its performance.

- **Drone Computer Vision for Obstacle Detection**: This part will concentrate on integrating computer vision techniques into the drone system. It aims to enable real-time obstacle detection and avoidance strategies, enhancing the drone's ability to navigate complex environments.

- **Integration of AI with System Components**: This section aims to unify AI algorithms with the previously developed system dynamics model, controller, and computer vision capabilities. It seeks to leverage AI to further enhance overall performance, responsiveness, and decision-making of the drone during Red Bull racing scenarios.

By splitting the report into these segments, it is expected to inspect each aspect thoroughly while maintaining a clear focus on how these components can synergize to enhance the capabilities of Red Bull racing drones.

**Follow the left sidebar for more!**


## 4 Drone Characteristics

{{< details "**Drone parameters** - click to expand" close >}}
{{< katex display >}}
\text{Mass } M : 0.475 \, \text{kg} \\
\text{Moment of inertia } J_{xx} : 2.2 \times 10^{-3} \, \text{kg} \cdot \text{m}^2 \\
\text{Moment of inertia } J_{yy} : 2.5 \times 10^{-3} \, \text{kg} \cdot \text{m}^2 \\
\text{Moment of inertia } J_{zz} : 4.5 \times 10^{-3} \, \text{kg} \cdot \text{m}^2 \\
\text{Arm length } b : 0.177 \, \text{m} \\
\text{Gravity } g : 9.81 \, \text{m/s}^2 \\
\text{Force (thrust) constant } K_T : 9.2 \times 10^{-6} \, \text{N} \cdot \text{(rad/s)}^{-2} \\
\text{Momentum constant } K_Q : 0.32 \times 10^{-6} \, \text{Nm} \cdot \text{(rad/s)}^{-2} \\
\text{Voltage constant } K_{\Omega} : 0.002 \, \text{(rad/s)}^{-1} \\
\text{Motor dynamics approximated by static gain} : \delta_i \approx K_{\Omega} \Omega_i \\
{{< /katex >}}
{{< /details>}}