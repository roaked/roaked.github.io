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
\dot{\tilde{x}} = A\tilde{x} + B\tilde{u} = \begin{bmatrix} \dot{\tilde{I}}_i \\ \dot{\tilde{\Omega}} \end{bmatrix} = \begin{bpmatrix} a_{11} & a_{12} \\ a_{21} & a_{22} \end{bmatrix} \begin{bmatrix} \tilde{I}_i \\ \Omega \end{bmatrix} + \begin{bmatrix} b_2 \\ 0 \end{bmatrix} \tilde{V}_m
{{< /katex >}}

{{< katex display >}}
\tilde{y} = C\tilde{x} + D\tilde{u} = \Omega, \quad i = [0 \, 1]
{{< /katex >}}

The parameters in matrices A and B are determined by the motor specifications and the chosen flight conditions for linearization.


#### 1.1.1 Model and Linearization

The actuation subsystem dynamics are defined by the following nonlinear equations:

{{< katex display >}}

\dot{I}_i = \frac(1)(L_m) (V_{mi} - R_{m}I_{i} - K_{e}\Omega_{i})

{{< /katex >}}  

{{< katex display >}}

\dot{\Omega}_i = \frac(1)(J_m) (K_t I_i - Q_i - B_m\Omega_{i})

{{< /katex >}}  

{{< katex display >}}

\text{Where:    } Q_i = K_Q \Omega_i^2

{{< /katex >}}  

To linearize this model, a nominal point around which small deviations are considered for the variables I, Vm and Ω is selected, representing the hovering condition:


{{< katex display >}}

I_i = I_o + \tilde{I}_i

{{< /katex >}}  

{{< katex display >}}

V_{mi} = V_{mo} + \tilde{V}_{mi}

{{< /katex >}}  

{{< katex display >}}

\Omega_i = \Omega_{io} + \tilde{\Omega}_{i}

{{< /katex >}}  

Using these relationships, the linearized dynamics are obtained as:

{{< katex display >}}

\dot{\tilde{I}}_i = \frac(1)(L_m) (\tilde{V}_{mi} - R_m \tilde{I}_i - K_e \tilde{\Omega}_i)

{{< /katex >}}  

{{< katex display >}}

\dot{\tilde{\Omega}}_i = \frac(1)(J_m) (K_t \tilde{I}_{i} - 2 K_Q \Omega_{io} \tilde{\Omega}_i - B_m \tilde{\Omega}_i)

{{< /katex >}}  

The linearized state space model for each actuation subsystem, where the output is the motor angular speed, is given by:


{{< katex display >}}
\begin{pmatrix}
\dot{I}_i \\
\dot{\Omega}_i
\end{pmatrix} = \begin{pmatrix}
-L_m R_m & J_m K_t \\
-L_m K_e & -J_m 2K_Q \Omega_{io} + B_m
\end{pmatrix} \begin{pmatrix}
I_i \\
\Omega_i
\end{pmatrix} + \begin{pmatrix}
L_{m1} \\
0
\end{pmatrix} \tilde{V}_{mi}
{{< /katex >}}  