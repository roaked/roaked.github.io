---
title: Drone Dynamics
weight: 2
---

# **System Dynamics**

# 1. System Dynamics Modeling 

Breaking down the drone into subsystems helps manage its complexity. The quadrotor has three main parts:

- Actuation System: This handles the rotors' model, the forces they generate ({{< katex >}}F_p{{< /katex >}}), and the moments they create ({{< katex >}}M_p{{< /katex >}}). Each rotor gets an input voltage ({{< katex >}}V_{m_i}{{< /katex >}}) and produces an angular velocity ({{< katex >}}\Omega_i{{< /katex >}}).

- Movement System: This part deals with forces (not just from rotors but also external factors like wind) and moments that drive the drone's movement. It uses physics laws, dynamics, and kinematics to determine the drone's position and velocity.

- Instrumentation System: This part involves the sensor model. In this project phase, the sensors are simplified as ideal sensors, meaning they have a gain of one.

Breaking it down this way helps in focusing on and understanding each aspect separately, making the overall understanding and development of the drone more manageable.

## 1.1 Actuation Subsystem

The actuation subsystem involves individual DC motors powering propellers to generate lift forces in the drone's four independently controlled motors (i=1,2,3,4). The values of I and {{< katex >}}\Omega{{< /katex >}} change over time based on the applied motor voltage, Vm, while constants like Lm, Rm, Ke, Kt, and Jm characterize the motor. This results in a second-order system for each drone motor, expressed as:

{{< katex display >}}
\dot{\tilde{x}} = A\tilde{x} + B\tilde{u} = \begin{bmatrix} \dot{\tilde{I}}_i \\ \dot{\tilde{\Omega}} \end{bmatrix} = \begin{bmatrix} a_{11} & a_{12} \\ a_{21} & a_{22} \end{bmatrix} \begin{bmatrix} \tilde{I}_i \\ \Omega \end{bmatrix} + \begin{bmatrix} b_2 \\ 0 \end{bmatrix} \tilde{V}_m
{{< /katex >}}

{{< katex display >}}
\tilde{y} = C\tilde{x} + D\tilde{u} = \Omega, \quad i = [0 \, 1]
{{< /katex >}}

The parameters in matrices A and B are determined by the motor specifications and the chosen flight conditions for linearization.


### 1.1.1 Model and Linearization

The actuation subsystem dynamics are defined by the following nonlinear equations:

{{< katex display >}}

\dot{I}_i = \frac{1}{L_m} (V_{mi} - R_{m}I_{i} - K_{e}\Omega_{i})

{{< /katex >}}  

{{< katex display >}}

\dot{\Omega}_i = \frac{1}{J_m} (K_t I_i - Q_i - B_m\Omega_{i})

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

\Omega_i = \Omega_{i_o} + \tilde{\Omega}_{i}

{{< /katex >}}  

Using these relationships, the linearized dynamics are obtained as:

{{< katex display >}}

\dot{\tilde{I}}_i = \frac{1}{L_m} (\tilde{V}_{m_i} - R_m \tilde{I}_i - K_e \tilde{\Omega}_i)

{{< /katex >}}  

{{< katex display >}}

\dot{\tilde{\Omega}}_i = \frac{1}{J_m} (K_t \tilde{I}_{i} - 2 K_Q \Omega_{i_o} \tilde{\Omega}_i - B_m \tilde{\Omega}_i)

{{< /katex >}}  

The linearized state space model for each actuation subsystem, where the output is the motor angular speed, is given by:


{{< katex display >}}
\begin{bmatrix}
\dot{I}_i \\
\dot{\Omega}_i
\end{bmatrix} = \begin{bmatrix}
-\frac{R_m}{L_m} & -\frac{K_e}{L_m} \\
\frac{K_t}{J_m} & -\frac{K_Q \Omega_{i_o}+B_m}{J_m}
\end{bmatrix} \begin{bmatrix}
\tilde{I_i} \\
\tilde{\Omega}_i
\end{bmatrix} + \begin{bmatrix}
\frac{1}{L_m} \\
0
\end{bmatrix} \tilde{V}_{mi}
{{< /katex >}}  

The state space variables are, therefore, {{< katex >}} \tilde{I}_i {{< /katex >}} and {{< katex >}} \tilde{\Omega}_{i} {{< /katex >}}. The only input provided to each motor is the applied voltage {{< katex >}} \tilde{V}_{m_{i}} {{< /katex >}}. The state space model for the complete actuation subsystem can be obtained by combining the state space models for each motor into a single state space model:

{{< katex display >}} 


\begin{bmatrix}
\dot{\tilde{I}}_1 \\
\dot{\tilde{\Omega}}_1 \\
\dot{\tilde{I}}_2 \\
\dot{\tilde{\Omega}}_2 \\
\dot{\tilde{I}}_3 \\
\dot{\tilde{\Omega}}_3 \\
\dot{\tilde{I}}_4 \\
\dot{\tilde{\Omega}}_4 \\
\end{bmatrix}
= \begin{bmatrix}
A_1 & 0 & 0 & 0 \\
0 & A_2 & 0 & 0 \\
0 & 0 & A_3 & 0 \\
0 & 0 & 0 & A_4 \\
\end{bmatrix}
\begin{bmatrix}
\tilde{I}_1 \\
\tilde{\Omega}_1 \\
\tilde{I}_2 \\
\tilde{\Omega}_2 \\
\tilde{I}_3 \\
\tilde{\Omega}_3 \\
\tilde{I}_4 \\
\tilde{\Omega}_4 \\
\end{bmatrix}
+
\begin{bmatrix}
B_1 & 0 & 0 & 0 \\
0 & B_2 & 0 & 0 \\
0 & 0 & B_3 & 0 \\
0 & 0 & 0 & B_4 \\
\end{bmatrix}
\begin{bmatrix}
\dot{\tilde{V}}_{m1} \\
0 \\
\dot{\tilde{V}}_{m2} \\
0 \\
\dot{\tilde{V}}_{m3} \\
0 \\
\dot{\tilde{V}}_{m4} \\
0 \\
\end{bmatrix} \\
{{< /katex >}}  

{{< katex display >}}
\begin{bmatrix}
\tilde{\Omega}_1 \\
\tilde{\Omega}_2 \\
\tilde{\Omega}_3 \\
\tilde{\Omega}_4 \\
\end{bmatrix}
= \begin{bmatrix}
0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
\tilde{I}_1 \\
\tilde{\Omega}_1 \\
\tilde{I}_2 \\
\tilde{\Omega}_2 \\
\tilde{I}_3 \\
\tilde{\Omega}_3 \\
\tilde{I}_4 \\
\tilde{\Omega}_4 \\
\end{bmatrix}

{{< /katex >}}  

Each matrix Ai and Bi represent a 2 × 2 matrix. As all four motors share identical parameters, the matrices Ai and Bi exhibit identical properties. However, due to the independent dynamics of each motor, the subsystem analysis simplifies. Subsequent sections will confirm the motors' rapid response times, allowing an approximation using a static gain, as demonstrated:

{{< katex display >}}
\delta_i = K_{\Omega} \Omega_i
{{< /katex >}}  

With this outcome in consideration, we can represent the angular velocities {{< katex >}}\Omega_i{{< /katex >}} solely as a function of the voltage constant {{< katex >}}K_{\Omega}_i{{< /katex >}} and the actuations {{< katex >}}\delta_i{{< /katex >}}

{{< katex display >}}
\begin{bmatrix}
\Omega_1 \\
\Omega_2 \\
\Omega_3 \\
\Omega_4 \\
\end{bmatrix}
= \begin{bmatrix}
1 & K_{\Omega} & 0 & 0 \\
0 & 1 & K_{\Omega} & 0 \\
0 & 0 & 1 & K_{\Omega} \\
0 & 0 & 0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
\delta_1 \\
\delta_2 \\
\delta_3 \\
\delta_4 \\
\end{bmatrix} \\
{{< /katex >}}  


This process essentially proportionally adjusts the input variables​ {{< katex >}}\delta_i{{< /katex >}}. Labeling the diagonal matrix as {{< katex >}}T_M{{< /katex >}} we can express this relationship by considering the linear deviations around the nominal operating point.


{{< katex display >}}

\tilde{\Omega} = T_M \tilde{\delta}
{{< /katex >}}  

### 1.1.2 Transfer Function Model

With the state space model acquired for the actuation subsystem, it is possible to derive the transfer function.

{{< katex display >}}
G(s) = C(sI - A)^{-1}B + D
{{< /katex >}}  

The relationship between the output (angular speed ​ {{< katex >}}\tilde{\Omega}_i{{< /katex >}}) and the input (applied voltage ​ {{< katex >}}\tilde{V}_{mi}{{< /katex >}}) is expressed as the transfer function:

{{< katex display >}}
G(s) = \frac{\tilde{\Omega}_i(s)}{\tilde{V}_{mi}(s)} = \begin{bmatrix} 0 & 1 \end{bmatrix} \begin{bmatrix} s + \frac{R_m}{L_m} & \frac{K_e}{L_m} \\ -\frac{K_t}{J_m} & s + \frac{2K_Q \Omega_{i_o} + B_m}{J_m}  \end{bmatrix}^{-1} \begin{bmatrix} 1 \\ \frac{1}{L_m} \\ 0 \end{bmatrix}
{{< /katex >}}  

When the aforementioned parameters are replaced with typical values for a DC motor, the resulting transfer function model can be represented.

{{< katex display >}}
G(s) = \frac{4.901 \times 10^6}{s^2 + 576.3s + 1.687 \times 10^5}
{{< /katex >}}  

### 1.1.3 Stability

To assess stability, the system poles need computation, achievable through two approaches. First, by determining the roots of the denominator the defined transfer function G(s), or second, by computing the eigenvalues of matrix A as depicted as follows:

{{< katex display >}}
\text{det}(sI - A) = s^2 + 576.3s + 1.687 \times 10^5 = 0 \quad \Leftrightarrow \quad s = -288.13 \pm 292.67i \quad (21)
{{< /katex >}}  

The poles, located in the left half-plane and forming complex conjugates, signify system stability. Expect an overshoot with a step input. The unit step response demonstrates a small overshoot ({{< katex >}}M_p{{< /katex >}} = 4.54%) and rapid system response ({{< katex >}}T_s{{< /katex >}} = 0.15s). Consequently, approximating the actuation subsystem as a static gain seems plausible. The root locus plot confirms system stability across various proportional controllers.

![step1](https://live.staticflickr.com/65535/53346502082_8cc9596829_c.jpg)


### 1.1.4 Controllability

The controllability of a state space model implies the capability to transition any initial state to a final state through a finite control action within a finite duration. This assurance exists when the rank of matrix C matches the dimension of the previously defined state vector, represented as n.
For the actuation subsystem, n = 2, and matrix {{< katex >}}\mathcal{C}{{< /katex >}} is thus expressed as follows. Upon observation, it's evident that C holds a rank of 2, affirming the system's controllability.

{{< katex display >}}
\mathcal{C} = [B \quad AB] = \begin{bmatrix} \frac{1}{Lm} & -\frac{Rm}{L^2m} \\ 0 & \frac{Kt}{Jm Lm} \end{bmatrix}
{{< /katex >}}


### 1.1.5 Observability

Observability of a state space model implies the ability to deduce any initial state from the system output within a given time interval. This certainty prevails when the rank of the observability matrix {{< katex >}}\mathcal{O}{{< /katex >}} matches the dimension of the state vector.

Given the state vector's dimension as n = 2, the observability matrix {{< katex >}}\mathcal{O}{{< /katex >}} is defined according to equation 23. Upon inspection, it's evident that the resulting matrix has a rank of 2, affirming the system's observability.

{{< katex display >}}
\mathcal{O} = \begin{bmatrix} C \\ CA \end{bmatrix} = \begin{bmatrix} 0 & 1 \\ \frac{K_t}{J_m} & -\frac{B_m + 2K_q \Omega_o}{J_m} \end{bmatrix} 
{{< /katex >}}

## 1.2 Movement Subsytem

To understand the movement subsystem, it's crucial to pinpoint the forces and moments within the system. The forces and moments are generated by the combined angular speeds of the rotors, as shown below:

{{< katex display >}}
T_i = K_T \Omega_i^2 
{{< /katex >}}

{{< katex display >}}
Q_i = K_Q \Omega_i^2 
{{< /katex >}}

{{< katex display >}}
F_p = \begin{bmatrix}
0 \\
0 \\
- \sum_{i=1}^{4} T_i \\
\end{bmatrix}
{{< /katex >}}

{{< katex display >}}
M_p = \begin{bmatrix}
b \cdot \cos(\pi/4) \cdot (T_1 - T_2 - T_3 + T_4) \\
b \cdot \cos(\pi/4) \cdot (T_1 + T_2 - T_3 - T_4) \\
-Q_1 + Q_2 - Q_3 + Q_4 \\
\end{bmatrix}
{{< /katex >}}

The equations for the dynamics and kinematics are the following:

{{< katex display >}}
m\dot{v} = -\omega \times mv + F_p + F_a + F_g \\
J\dot{\omega} = -\omega \times J\omega + M_p  \\
\dot{p} = Rv\\
\dot{\Phi} = S\omega
{{< /katex >}}

Considering the four angular velocities from the motors as inputs {{< katex >}}\tilde{u} = \tilde{\Omega} = [\tilde{\Omega}_1, \tilde{\Omega}_2, \tilde{\Omega}_3, \tilde{\Omega}_4]{{< /katex >}} and the twelve variables denoting both the linear and angular positions and velocities as the state {{< katex >}}X = [\tilde{v}^T, \tilde{\omega}^T, \tilde{p}^T, \tilde{\Phi}^T]^T{{< /katex >}}, the model is then represented:

{{< katex display >}}
\begin{bmatrix} \dot{\tilde{v}} \\ \dot{\tilde{\omega}} \\ \dot{\tilde{p}} \\ \dot{\tilde{\Phi}} \\ \end{bmatrix}
= \begin{bmatrix} 0 & 0 & 0 & A_V \\ 0 & 0 & 0 & 0 \\ I_3 & 0 & 0 & 0 \\ 0 & I_3 & 0 & 0 \\ \end{bmatrix}
\begin{bmatrix} \tilde{v} \\ \tilde{\omega} \\ \tilde{p} \\ \tilde{\Phi} \\ \end{bmatrix}
+
\begin{bmatrix} B_V \\ B_{\omega} \\ 0 \\ 0 \\ \end{bmatrix}
\tilde{\Omega}
{{< /katex >}}

Where Av, Bv, Bω are given by:

{{< katex display >}}
A_V = \begin{bmatrix} 0 & -g & 0 \\ g & 0 & 0 \\ 0 & 0 & 0 \\ \end{bmatrix}, \quad B_V = \begin{bmatrix} 0 & 0 & 0 & 0 \\ g & 0 & 0 & 0 \\ -b_z -b_z -b_z -b_z \end{bmatrix}, \quad B_{\omega} = \begin{bmatrix} 0 & -b_p & 0 & b_p \\ b_q & 0 & -b_q & 0 \\ -b_q & b_p & -b_p & b_p \\ \end{bmatrix}
{{< /katex >}}

The coefficients for the matrix {{< katex >}}B_V{{< /katex >}} and {{< katex >}}B_{\omega}{{< /katex >}} depend on the parameters of the quad rotor and flight conditions. But combining previous equations, it is obtained:

{{< katex display >}}
\begin{bmatrix}
\dot{\tilde{v}}_x \\
\dot{\tilde{v}}_y \\
\dot{\tilde{v}}_z \\
\dot{\tilde{\omega}}_x \\
\dot{\tilde{\omega}}_y \\
\dot{\tilde{\omega}}_z \\
\dot{\tilde{p}}_x \\
\dot{\tilde{p}}_y \\
\dot{\tilde{p}}_z \\
\dot{\tilde{\phi}} \\
\dot{\tilde{\theta}} \\
\dot{\tilde{\psi}} \\
\end{bmatrix}
= \begin{bmatrix}
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & -g & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & g & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 & 0 \\
\end{bmatrix}
\begin{bmatrix}
\tilde{v}_x \\
\tilde{v}_y \\
\tilde{v}_z \\
\tilde{\omega}_x \\
\tilde{\omega}_y \\
\tilde{\omega}_z \\
\tilde{p}_x \\
\tilde{p}_y \\
\tilde{p}_z \\
\tilde{\phi} \\
\tilde{\theta} \\
\tilde{\psi} \\
\end{bmatrix}
+
\begin{bmatrix}
0 & 0 & 0 & 0 \\
g & 0 & 0 & 0 \\
-bz & -bz & -bz & -bz \\
0 & -bp & 0 & bp \\
bq & 0 & -bq & 0 \\
-bq & bp & -bp & bp \\
0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 \\
\end{bmatrix}
\begin{bmatrix}
\tilde{\Omega}_1 \\
\tilde{\Omega}_2 \\
\tilde{\Omega}_3 \\
\tilde{\Omega}_4 \\
\end{bmatrix}
{{< /katex >}}

It is revelant to note that another possibility would be to divide the quad rotor dynamics into six subsystems, instead of a big matrix. Following up the state equation, the output would translate to:


{{< katex display >}}
y = \begin{bmatrix} 0 & 0 & I_3 & 0 \\ 0 & 0 & 0 & I_3 \\ \end{bmatrix} \begin{bmatrix} \tilde{v} \\ \tilde{\omega} \\ \tilde{p} \\ \tilde{\Phi} \\ \end{bmatrix} = \begin{bmatrix} \tilde{p} \\ \tilde{\Phi} \\ \end{bmatrix}

{{< /katex >}}

Which would expand to:

{{< katex display >}}
\begin{bmatrix}
0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 \\
\end{bmatrix}
\begin{bmatrix}
\tilde{v}_x \\
\tilde{v}_y \\
\tilde{v}_z \\
\tilde{\omega}_x \\
\tilde{\omega}_y \\
\tilde{\omega}_z \\
\tilde{p}_x \\
\tilde{p}_y \\
\tilde{p}_z \\
\tilde{\phi} \\
\tilde{\theta} \\
\tilde{\psi} \\
\end{bmatrix}
=\begin{bmatrix}
\tilde{p}_x \\
\tilde{p}_y \\
\tilde{p}_z \\
\tilde{\phi} \\
\tilde{\theta} \\
\tilde{\psi} \\
\end{bmatrix}
{{< /katex >}}

### 1.2.1 Model Simplification and Analysis
