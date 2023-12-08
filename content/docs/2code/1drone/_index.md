---
title: UAV Red Bull Air Racing
weight: 1
---

# **An Autonomous Drone Racing Competition**

## 1 RedBull Racing 

The world of Red Bull drone racing stands at the intersection of high-speed competition and very advanced technology. In this exhilarating arena, unmanned aerial vehicles (UAVs) navigate intricate courses filled with obstacles, pushing the limits of flight capabilities and piloting skills. To revolutionize this dynamic sport, I developped a model that aims to integrate advanced AI, computer vision, and simulated dynamics to elevate drone performance and safety within the competitive realm.

![redbulll](https://www.dronetrest.com/uploads/db5290/original/2X/f/fbcf6fcd68bc57fac9f38ce5148e43f1cb95b926.png)

## 2 Overview

This interesting concept revolves around the development of a comprehensive system that seamlessly combines state-of-the-art technologies to enhance Red Bull drone racing. It is desired to fuse AI-driven algorithms, computer vision systems, and simulated dynamics modeling to optimize drone navigation through complex racecourses.



{{< hint info >}}
**Topics:**

- **Computer Vision or AI-Enabled Obstacle Detection and Avoidance**: Implement sophisticated AI algorithms coupled with computer vision systems onboard the drones. These systems will enable real-time obstacle detection and swift maneuvering to avoid collisions during high-speed races.

- **Simulated Dynamics Modeling**: Develop intricate models that encapsulate the intricate dynamics of the drones, including aerodynamics, motor responses, and environmental interactions. This simulation will accurately predict drone behaviour in various scenarios, aiding in performance optimization.

- **Simulink Integration via TCP/IP**: Establish a robust connection between the simulated drone dynamics model in Simulink and the actual drones using TCP/IP. This connection facilitates real-time communication, enabling testing and refinement of control algorithms in a virtual environment. This will be done by accessing an external library. {{< /hint >}}

As previously mentioned, this approach enhances drone maneuverability and agility when navigating intricate racecourses, elevates safety by detecting and avoiding obstacles in real time, and fine-tunes control algorithms for high-speed racing situations.

## 3 Work Division

Given how complex this project turned out to be, I have decided to structure it into distinct sections to comprehensively cover various aspects of the system design:

{{< hint info >}}
- **Drone System Dynamics Modeling and Controller Design**: This section will focus on developing and analyzing models for the drone's dynamics. It will delve into understanding how the drone behaves under different conditions and designing an appropriate controller to optimize its performance. The foundation of this section of the work draws heavily from the theoretical knowledge acquired from an external MOOC (*Massive Open Online Course*), developed by Prof. Alexandra Moutinho and Prof. José Azinheira, accessible [by clicking here.](https://courses.elearning.tecnico.ulisboa.pt/courses/course-v1:MOOCs+droneX+2021/about#en%20translation)

- **Drone Computer Vision for Obstacle Detection**: This part will concentrate on integrating computer vision techniques into the drone system. It aims to enable real-time obstacle detection and avoidance strategies, enhancing the drone's ability to navigate complex environments.

- **Integration of AI with System Components**: This section aims to unify AI algorithms with the previously developed system dynamics model, controller, and computer vision capabilities. It seeks to leverage AI to further enhance overall performance, responsiveness, and decision-making of the drone during Red Bull racing scenarios.{{< /hint >}}

By splitting the report into these segments, it is expected to inspect each aspect thoroughly while maintaining a clear focus on how these components can synergize to enhance the capabilities of Red Bull racing drones.

## 4 Reading Guide

{{< hint tip >}}
The sections are pretty extensive, containing descriptive explaination of the work done, but I have inserted an hyperlink at the beginning of each section redirecting to the last chapter containing summarized findings. 
{{< /hint >}}

![video](https://s5.gifyu.com/images/SRQm0.gif)

{{< hint tip >}}
[That said: Advance to UAV System Dynamics Design Here ✌️](https://ricardochin.com/docs/2code/1drone/_index2/){{< /hint >}}

## 5 Drone Characteristics 

{{< details "**Drone Physical Properties:** - (click to expand)" close >}}
| Parameter                           | Value               | Unit         |
|-------------------------------------|---------------------|--------------|
| Mass M                          | 0.475               | kg           |
| Moment of inertia J_{xx}        | 2.2 x 10^{-3} | kg.m^2   |
| Moment of inertia J_{yy}        | 2.5 x 10^{-3} | kg.m^2   |
| Moment of inertia J_{zz}        | 4.5 x 10^{-3} | kg.m^2   |
| Arm length b                    | 0.177               | m            |
| Gravity g                       | 9.81                | m/s^2    |
| Force (thrust) constant K_t     | 9.2 x 10^{-6} | N(rad/s)^{-2} |
| Momentum constant K_q          | 0.32 x 10^{-6}| Nm(rad/s)^{-2} |
| Voltage constant K_Ω     | 0.002               | (rad/s)^{-1} |

Motor dynamics approximated by static gain : δi ~  KΩ x Ωi
{{< /details >}}





