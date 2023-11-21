---
title: Drone Dynamics & Controller
weight: 2
---

# System Dynamics & Controller 

## 1 System Dynamics Modeling 

Breaking down the drone into subsystems helps manage its complexity. The quadrotor has three main parts:

- Actuation System: This handles the rotors' model, the forces they generate (Fp), and the moments they create (Mp). Each rotor gets an input voltage (Vmi) and produces an angular velocity ({{< katex >}}\Omega{{< /katex >}} i).

- Movement System: This part deals with forces (not just from rotors but also external factors like wind) and moments that drive the drone's movement. It uses physics laws, dynamics, and kinematics to determine the drone's position and velocity.

- Instrumentation System: This part involves the sensor model. In this project phase, the sensors are simplified as ideal sensors, meaning they have a gain of one.

Breaking it down this way helps in focusing on and understanding each aspect separately, making the overall understanding and development of the drone more manageable.

### 1.1 Actuation Subsystem

The actuation subsystem involves individual DC motors powering propellers to generate lift forces in the drone's four independently controlled motors (i=1,2,3,4). The values of I and {{< katex >}}\Omega{{< /katex >}} change over time based on the applied motor voltage, Vm, while constants like Lm, Rm, Ke, Kt, and Jm characterize the motor. This results in a second-order system for each drone motor, expressed as:

{{< katex display >}}
\dot{\tilde{x}} = A\tilde{x} + B\tilde{u} = \begin{pmatrix} \dot{\tilde{I}}_i \\ \dot{\tilde{\Omega}} \end{pmatrix} = \begin{pmatrix} a_{11} & a_{12} \\ a_{21} & a_{22} \end{pmatrix} \begin{pmatrix} \tilde{I}_i \\ \Omega \end{pmatrix} + \begin{pmatrix} b_2 \\ 0 \end{pmatrix} \tilde{V}_m
{{< /katex >}}

{{< katex display >}}
\tilde{y} &= C\tilde{x} + D\tilde{u} = \Omega, \quad i = [0 \, 1]
\end{align*} \]
{{< /katex >}}

The parameters in matrices A and B are determined by the motor specifications and the chosen flight conditions for linearization.