---
title: Drone System Dynamics
weight: 2
---

# **Phase 1. Drone System Dynamics**

{{< hint tip >}}
If you wish to skip the theoretical and implementation details, [please click here to jump to the summarized findings Ch. 4 - The Moving UAV](https://ricardochin.com/docs/2code/1drone/_index2/#4-the-moving-uav).{{< /hint >}}

## 1 System Dynamics Modeling 

Breaking down the drone into subsystems helps manage its complexity. The quadrotor has three main parts:

- Actuation System: This handles the rotors' model, the forces they generate ({{< katex >}}F_p{{< /katex >}}), and the moments they create ({{< katex >}}M_p{{< /katex >}}). Each rotor gets an input voltage ({{< katex >}}V_{m_i}{{< /katex >}}) and produces an angular velocity ({{< katex >}}\Omega_i{{< /katex >}}).

- Movement System: This part deals with forces (not just from rotors but also external factors like wind) and moments that drive the drone's movement. It uses physics laws, dynamics, and kinematics to determine the drone's position and velocity.

- Instrumentation System: This part involves the sensor model. In this project phase, the sensors are simplified as ideal sensors, meaning they have a gain of one.

Breaking it down this way helps in focusing on and understanding each aspect separately, making the overall understanding and development of the drone more manageable.

### 1.1. Actuation Subsystem

The actuation subsystem involves individual DC motors powering propellers to generate lift forces in the drone's four independently controlled motors (i=1,2,3,4). The values of I and {{< katex >}}\Omega{{< /katex >}} change over time based on the applied motor voltage, Vm, while constants like Lm, Rm, Ke, Kt, and Jm characterize the motor. This results in a second-order system for each drone motor, expressed as:

{{< katex display >}}
\dot{\tilde{x}} = A\tilde{x} + B\tilde{u} = \begin{bmatrix} \dot{\tilde{I}}_i \\ \dot{\tilde{\Omega}} \end{bmatrix} = \begin{bmatrix} a_{11} & a_{12} \\ a_{21} & a_{22} \end{bmatrix} \begin{bmatrix} \tilde{I}_i \\ \Omega \end{bmatrix} + \begin{bmatrix} b_2 \\ 0 \end{bmatrix} \tilde{V}_m
{{< /katex >}}

{{< katex display >}}
\tilde{y} = C\tilde{x} + D\tilde{u} = \Omega, \quad i = [0 \, 1]
{{< /katex >}}

The parameters in matrices A and B are determined by the motor specifications and the chosen flight conditions for linearization.


#### 1.1.1. Model and Linearization

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
0 \\
\end{bmatrix} \tilde{V}_{mi}
{{< /katex >}}  

The state space variables are, therefore, {{< katex >}}\tilde{I}_i{{< /katex >}} and {{< katex >}} \tilde{\Omega}_{i}{{< /katex >}}. The only input provided to each motor is the applied voltage {{< katex >}} \tilde{V}_{m_{i}} {{< /katex >}}. The state space model for the complete actuation subsystem can be obtained by combining the state space models for each motor into a single state space model:

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

With this outcome in consideration, the angular velocities {{< katex >}}\Omega_i{{< /katex >}} can be presented solely as a function of the voltage constant {{< katex >}}K_{\Omega_{i}}{{< /katex >}} and the actuations {{< katex >}}\delta_i{{< /katex >}}

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


This process essentially proportionally adjusts the input variables​ {{< katex >}}\delta_i{{< /katex >}}. Labeling the diagonal matrix as {{< katex >}}T_M{{< /katex >}}, this relationship can be expressed by considering the linear deviations around the nominal operating point.


{{< katex display >}}

\tilde{\Omega} = T_M \tilde{\delta}
{{< /katex >}}  

#### 1.1.2. Transfer Function Model

With the state space model acquired for the actuation subsystem, it is possible to derive the transfer function.

{{< katex display >}}
G(s) = C(sI - A)^{-1}B + D
{{< /katex >}}  

The relationship between the output (angular speed ​ {{< katex >}}\tilde{\Omega}_i{{< /katex >}}) and the input (applied voltage ​ {{< katex >}}\tilde{V}_{mi}{{< /katex >}}) is expressed as the transfer function:

{{< katex display >}}
G(s) = \frac{\tilde{\Omega}_i(s)}{\tilde{V}_{mi}(s)} = \begin{bmatrix} 0 & 1 \end{bmatrix} \begin{bmatrix} s + \frac{R_m}{L_m} & \frac{K_e}{L_m} \\ -\frac{K_t}{J_m} & s + \frac{2K_Q \Omega_{i_o} + B_m}{J_m}  \end{bmatrix}^{-1} \begin{bmatrix} 1 \\ \frac{1}{L_m} \\ 0 \\ \end{bmatrix}
{{< /katex >}}  

When the aforementioned parameters are replaced with typical values for a DC motor, the resulting transfer function model can be represented.

{{< katex display >}}
G(s) = \frac{4.901 \times 10^6}{s^2 + 576.3s + 1.687 \times 10^5}
{{< /katex >}}  

#### 1.1.3. Stability

To assess stability, the system poles need computation, achievable through two approaches. First, by determining the roots of the denominator the defined transfer function G(s), or second, by computing the eigenvalues of matrix A as depicted as follows:

{{< katex display >}}
\text{det}(sI - A) = s^2 + 576.3s + 1.687 \times 10^5 = 0 \quad \Leftrightarrow \quad s = -288.13 \pm 292.67i \quad (21)
{{< /katex >}}  

The poles, located in the left half-plane and forming complex conjugates, signify system stability. Expect an overshoot with a step input. The unit step response demonstrates a small overshoot ({{< katex >}}M_p{{< /katex >}} = 4.54%) and rapid system response ({{< katex >}}T_s{{< /katex >}} = 0.15s). Consequently, approximating the actuation subsystem as a static gain seems plausible. The root locus plot confirms system stability across various proportional controllers.

![step1](https://live.staticflickr.com/65535/53346502082_8cc9596829_c.jpg)


#### 1.1.4. Controllability

The controllability of a state space model implies the capability to transition any initial state to a final state through a finite control action within a finite duration. This assurance exists when the rank of matrix C matches the dimension of the previously defined state vector, represented as n.
For the actuation subsystem, n = 2, and matrix {{< katex >}}\mathcal{C}{{< /katex >}} is thus expressed as follows. Upon observation, it's evident that C holds a rank of 2, affirming the system's controllability.

{{< katex display >}}
\mathcal{C} = [B \quad AB] = \begin{bmatrix} \frac{1}{Lm} & -\frac{Rm}{L^2m} \\ 0 & \frac{Kt}{Jm Lm} \\ \end{bmatrix}
{{< /katex >}}


#### 1.1.5. Observability

Observability of a state space model implies the ability to deduce any initial state from the system output within a given time interval. This certainty prevails when the rank of the observability matrix {{< katex >}}\mathcal{O}{{< /katex >}} matches the dimension of the state vector.

Given the state vector's dimension as n = 2, the observability matrix {{< katex >}}\mathcal{O}{{< /katex >}} is defined according to equation 23. Upon inspection, it's evident that the resulting matrix has a rank of 2, affirming the system's observability.

{{< katex display >}}
\mathcal{O} = \begin{bmatrix} C \\ CA \end{bmatrix} = \begin{bmatrix} 0 & 1 \\ \frac{K_t}{J_m} & -\frac{B_m + 2K_q \Omega_o}{J_m} \\ \end{bmatrix} 
{{< /katex >}}

### 1.2. Movement Subsytem

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
A_V = \begin{bmatrix} 0 & -g & 0 \\ g & 0 & 0 \\ 0 & 0 & 0 \\ \end{bmatrix}, \quad B_V = \begin{bmatrix} 0 & 0 & 0 & 0 \\ g & 0 & 0 & 0 \\ -b_z & -b_z & -b_z & -b_z \\ \end{bmatrix}, \quad B_{\omega} = \begin{bmatrix} 0 & -b_p & 0 & b_p \\ b_q & 0 & -b_q & 0 \\ -b_q & b_p & -b_p & b_p \\ \end{bmatrix}
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
+\\
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

#### 1.2.1. Simplification and Analysis

The analysis of the movement subsystem can be simplified by approximating the motor dynamics by a static gain. This means it is possible to change the matrix B such that the actuation variables {{< katex >}}\delta_i{{< /katex >}} becomes the inputs instead of the motor angular speeds {{< katex >}}\Omega_i{{< /katex >}}. This transformation is expressed:

{{< katex display >}}
\dot{X} = AX + B\tilde{\Omega} = AX + BT_M \tilde{\delta}
{{< /katex >}}

The matrix {{< katex >}}T_M{{< /katex >}} represents the voltage constant ({{< katex >}}K\Omega{{< /katex >}}) as depicted previously. This adjustment does not alter the subsequent analysis; it merely scales the inputs. However, this transformation proves valuable for future use when implementing a controller.


#### 1.2.2. Stability

The stability assessment involves determining the system's poles, achieved by computing the eigenvalues of matrix A derived previously, encompassing the entire movement subsystem. Upon computation in Matlab, it reveals that all 12 eigenvalues, corresponding to the poles, are zero, indicating system instability.

#### 1.2.3. Controllability 

In order to assess the controllability of a state space model, it is necessary to compute its controllability matrix. When considering the entire movement subsystem, the controllability matrix {{< katex >}}\mathcal{C}{{< /katex >}} is defined:

{{< katex display >}}
\mathcal{C} = \begin{bmatrix}
B & AB & A^2B & A^3B & A^4B & A^5B & A^6B & A^7B & A^8B & A^9B & A^{10}B & A^{11}B
\end{bmatrix}
{{< /katex >}}

Upon conducting a rank computation of this matrix in Matlab, it is established that it holds a rank of 12. Consequently, indicating the controllability of the system.


#### 1.2.3. Observability

When assessing observability, the rank of the observability matrix {{< katex >}}\mathcal{O}{{< /katex >}} is computed. Considering the entirety of the movement subsystem, the matrix {{< katex >}}\mathcal{O}{{< /katex >}} is represented as:

{{< katex display >}}
\mathcal{O} = \begin{bmatrix}
C \\
CA \\
\vdots \\
CA^{11} \\
\end{bmatrix}
{{< /katex >}}

This matrix has also rank 12 and, therefore, the system is observable.

## 2 State Feedback Control

Previously, it was derived a model for the UAV, enabling to create linear and nonlinear simulators in Simulink. The next step is to implement a state feedback control system that allows the UAV to both follow and maintain a specified reference trajectory.

To effectively control the UAV, I aim to manage its lateral movement ({{< katex >}}P_N{{< /katex >}} and {{< katex >}}P_E{{< /katex >}}), vertical movement ({{< katex >}}P_D{{< /katex >}}), and yaw angle ({{< katex >}}\psi{{< /katex >}}). Thus, it is needed an adjustment of the matrix C to exclusively capture these controlled states in the output. Consequently, the updated matrix C will be tailored to reflect only these specific states, ensuring the control system targets the intended dynamics.

{{< katex display >}}
C =\begin{bmatrix}
0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 & 0 & 0 & 0 \\
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 1 \\
\end{bmatrix}
{{< /katex >}}

Upon computing the resulting observability matrix, it was verified that its rank remained at 12, indicating the system's sustained observability. Given the interest in tracking a specific reference while ensuring resilience against disturbances, a servo feedback system emerges as a promising choice. To achieve this, it necessitates employing the entire state vector in feedback, thereby requiring the ability to measure all 12 variables within the state vector. This measurement can be achieved through physical sensors or, alternatively, using observer techniques. For the current phase, it is assumed that reliable measurements of all system states are available.

A servo feedback control system exhibits the structure illustrated onwards. To actualize this closed-loop configuration, the values of K and {{< katex >}}K_i{{< /katex >}} need computation. This process commences by deriving the augmented open-loop system.

{{< katex display >}}
\begin{bmatrix}
\dot{x}(t) \\
\dot{x}_i(t) \\
\end{bmatrix}
= \begin{bmatrix}
A & 0 \\
-C & 0 \\
\end{bmatrix}
\begin{bmatrix}
x(t) \\
x_i(t) \\
\end{bmatrix}
+
\begin{bmatrix}
B \\
0 \\
\end{bmatrix}
u(t) +
\begin{bmatrix}
0 \\
1 \\
\end{bmatrix}
r
{{< /katex >}}

![servosystem](https://live.staticflickr.com/65535/53348383011_9d456b6a77_c.jpg)

{{< katex display >}}
y(t) = \begin{bmatrix}
C & 0 \\
\end{bmatrix}
\begin{bmatrix}
x(t) \\
x_i(t) \\
\end{bmatrix}

\bar{A} = \begin{bmatrix}
A & 0 \\
-C & 0 \\
\end{bmatrix}
, \bar{B} = \begin{bmatrix}
B \\
0 \\
\end{bmatrix}
, \bar{C} = [C  0] 
{{< /katex >}}


Based on the previously obtained model, {{< katex >}}\bar{A}{{< /katex >}} is a 16x16 matrix, {{< katex >}}\bar{B}{{< /katex >}} is a 16x4 matrix and {{< katex >}}\bar{C}{{< /katex >}} is a 4x16 matrix. The next step involves determining the gains K and {{< katex >}}k_i{{< /katex >}} to design a controller ensuring the asymptotic stability of the system.

{{< katex display >}}
u(t) = - \begin{bmatrix}
K & k_i \\
\end{bmatrix}
\begin{bmatrix}
x(t) \\
x_i(t) \\
\end{bmatrix}


\begin{bmatrix}
\dot{x}(t) \\
\dot{x}_i(t) \\
\end{bmatrix}
=\begin{bmatrix}
A - BK & Bk_i \\
-C & 0 \\
\end{bmatrix}
\begin{bmatrix}
x(t) \\
x_i(t) \\
\end{bmatrix}
+
\begin{bmatrix}
0 \\
1 \\
\end{bmatrix}
r
{{< /katex >}}

To determine the entries of K and {{< katex >}}K_i{{< /katex >}}, selecting the desired new pole locations was conducted through a trial-and-error approach. The aim was to achieve a rapid response while minimizing oscillations and conserving actuator energy. After identifying the new pole locations, the computation of K and {{< katex >}}K_i{{< /katex >}} was performed using the [MATLAB function 'place'](https://de.mathworks.com/help/control/ref/place.html), which generates a 4x16 matrix  {{< katex >}}\bar{K}{{< /katex >}} enmpassing all the entries, from which a 4x12 matrix {{< katex >}}\bar{K}{{< /katex >}} and a 4x4 matrix {{< katex >}}\bar{K}_i{{< /katex >}} were subsequently derived.

{{< katex display >}}
\bar{K} = [K | K_i]
{{< /katex >}}

The upcoming section will involve testing the behaviour of both the linear and nonlinear models when employing the acquired controller.

## 3 Simulation Results 

To assess system performance, two Simulink models were developed. The linear model represents the state-space model derived from a linearization that assumes a hovering condition where the drone's weight is offset by the four propulsion forces. On the other hand, the nonlinear model, implements dynamic and kinematic equations alltogether.

Following an iterative process, desired poles were obtained through trial and error. These poles were crucial for computing the K and {{< katex >}}K_i{{< /katex >}} matrices, enabling the construction of a controller for both models. This controller transformed them into servo systems with state feedback control. This section focuses on analyzing the performance of the servo feedback control in both simulators, along with evaluating the controller's robustness against disturbances.

{{< details "**MATLAB Simulink:** Linear Model - (click to expand)" close >}}
![linear](https://live.staticflickr.com/65535/53348849255_e94784ca08_c.jpg)
{{< /details >}}

{{< details "**MATLAB Simulink:** Non-linear Model - (click to expand)" close >}}
![nonlinear](https://live.staticflickr.com/65535/53348722664_0db04f6f60_z.jpg)
{{< /details >}}

### 3.1. Linear Model Results

As previously stated, the linear model was linearized under the assumption of a hovering state. In the forthcoming simulations, the model will undergo two combined movements: a vertical and yaw movement, as well as a vertical and lateral movement. Each simulation will present data on position, yaw angle, and actuation for the UAV.

#### 3.1.1. Vertical and Yaw Movement

Combining the vertical climb with a yaw movement is equivalent to introducing a negative P{{< katex >}}D_{ref}{{< /katex >}} alongside a specified final yaw angle value {{< katex >}}\Psi_{ref}{{< /katex >}}.


{{< details "**Vertical and Yaw Movement:** Movement of Z and Yaw - (click to expand)" close >}}
![Movement Z/YAW angle for the vertical and yaw movement](https://live.staticflickr.com/65535/53348641823_a9a1713d57_c.jpg)
{{< /details >}}

It illustrates the drone adeptly tracking the given references without encountering any noticeable issues. However, the next figure indicates a minimal, yet inconsequential impact on the lateral position ({{< katex >}}P_N{{< /katex >}} and {{< katex >}}P_E{{< /katex >}}), noted as exceedingly small ({{< katex >}}10^{-4}{{< /katex >}}).

{{< details "**Vertical and Yaw Movement:** Movement of X and Y - (click to expand)" close >}}
![Movement of X and Y for the vertical and yaw movement](https://live.staticflickr.com/65535/53348733609_655920023c_c.jpg)
{{< /details >}}

As anticipated, the activation of all four motors caused the UAV to ascend vertically. Notably, motors 1 and 3, as well as motors 2 and 4, exhibited varying actuations, leading to a variation in the yaw angle.

{{< details "**Vertical and Yaw Movement:** Actuations - (click to expand)" close >}}
![Actuations for the vertical and yaw movement](https://live.staticflickr.com/65535/53348860180_30a3b15b6f_c.jpg)
{{< /details >}}

#### 3.1.1. Vertical and Lateral Movement

For the second movement, a decision was made to combine forward and vertical positioning for the drone. The following figure illustrates the UAV's stabilization on the specified reference, although initial oscillations in the vertical position are evident. As predicted, the system eventually reaches the final position dictated by the reference, affirming the seamless functionality of the servo controller.

{{< details "**Vertical and Lateral Movement:** Movement of X and Z - (click to expand)" close >}}
![Movement Z/YAW angle for the vertical and lateral movement](https://live.staticflickr.com/65535/53347535807_3c277b14e9_c.jpg)
{{< /details >}}

Additionally, the next figure indicates a minimal PE movement ({{< katex >}}10^{-3}{{< /katex >}}), aligning with expectations.

{{< details "**Vertical and Lateral Movement:** Movement of Y - (click to expand)" close >}}
![Movement of X and Y for the vertical and lateral movement](https://live.staticflickr.com/65535/53348733604_9870b7b0de_c.jpg)
{{< /details >}}

The resulting actuation of the four motors can also be seen:

{{< details "**Vertical and Lateral Movement:** Actuations - (click to expand)" close >}}
![Actuations for the vertical and yaw movement](https://live.staticflickr.com/65535/53348641828_c6483ca067_c.jpg)
{{< /details >}}

### 3.2. Non-linear Model Results

The nonlinear model exhibited increased instability during testing compared to the linear model. Some simulations encountered errors due to numerical overflow, caused by large pitch and roll angles attempting to track references.

This instability arose from significant pitch or roll angles inducing high speeds, leading to the loss of lift forces from the propellers and the drone's descent. To focus on testing the servo controller, smaller references were used. Alternatively, employing ramp-like references or smaller steps instead of large singular steps may mitigate extreme responses in the nonlinear model.

#### 3.2.1. Vertical and Yaw Movement

In this simulation, the given reference involves ascending while simultaneously rotating on the yaw angle. The upcoming figure illustrates the successful tracking of the reference without any issues. 

{{< details "**Vertical and Yaw Movement:**  Movement of Z and yaw - (click to expand)" close >}}
![vertical and yaw movement](https://live.staticflickr.com/65535/53348910605_c73dcf8dbb_z.jpg)
{{< /details >}}

Similar to observations in the linear model, there was a minor impact on the lateral position, as depicted:

{{< details "**Lateral Movement:**  Movement of X and Y - (click to expand)" close >}}
![asdaudasmovement](https://live.staticflickr.com/65535/53348782754_47dc807d96_c.jpg)
{{< /details >}}

As anticipated, all motors were activated to elevate the altitude, with motors 2 and 4 exhibiting substantial actuation to align the yaw angle with the provided reference. 

{{< details "**Vertical and Lateral Movement:** Motor Actuations - (click to expand)" close >}}
![asdaudasmosfafavement](https://live.staticflickr.com/65535/53348691623_08cba40fc3_c.jpg)
{{< /details >}}

#### 3.2.2. Vertical and Lateral Movement

In this scenario, the reference entails ascending while moving forward. It is shown the drone adeptly tracking the lateral reference, albeit with slight oscillations. However, despite stabilizing approximately 7 seconds later, the oscillations around the final altitude exceed acceptable limits.

{{< details "**Vertical and Lateral Movement:**  Movement of Z and X - (click to expand)" close >}}
![verticadal anddsada yaw movement](https://live.staticflickr.com/65535/53348691628_3a4014b79c_c.jpg)
{{< /details >}}

{{< details "**Lateral Movement:**  Movement of Y - (click to expand)" close >}}
![verticadalsad anddsada yaw movement](https://live.staticflickr.com/65535/53348910585_d5dda5e464_c.jpg)
{{< /details >}}

{{< details "**Vertical and Lateral Movement:** Motor Actuations - (click to expand)" close >}}
![asdaudsfafafasmosfafavement](https://live.staticflickr.com/65535/53348910595_a20c9e4c7f_c.jpg)
{{< /details >}}

Upon reviewing the actuation of the four motors, it's evident that the actuation exhibited chaotic behaviour. This suggests that the controller values require correction and fine-tuning to enable smoother tracking of the provided reference, eliminating substantial oscillations.


### 3.3. Robustness to Disturbances

In this section, the focus lies on evaluating the system's response to disturbances. Specifically, the goal is to test its resilience against input disturbances. These disturbances entail unexpected alterations in the control action for select motors. This situation compels the servo control system to adapt and uphold the designated reference position and yaw angle.


- Yaw Compensation - Motors 2 and 4
- Altitude Compensation - All Motors

## 4 The Moving UAV


This section aimed to derive and analyze a realistic quadcopter model. Initially, it involved establishing the dynamics of two subsystems: actuation (comprising four motors and propellers) and movement (relating actuation forces to drone dynamics and kinematics).

![aa](https://live.staticflickr.com/65535/53355156265_947fd93796_c.jpg)

Once the complete model was obtained, it was initially simplified with a static gain assumption in the actuation subsystem. Subsequent analysis involved studying stability, observability, and controllability. The model, despite lacking stability, proved to be both observable and controllable, indicating feasibility for implementing a feedback control system.

![bb](https://live.staticflickr.com/65535/53355032634_4dae3c12da_c.jpg)

The model's characteristics indicated the need to control three coordinates and the yaw angle for movement regulation. Consequently, a servo feedback control system was developed and tested using both linear and nonlinear models. While the controller allowed the model to track given references, outcomes varied—excessive oscillations and slower response times were evident, notably in the nonlinear model.

The nonlinear simulation revealed challenges where large references risked crashing the simulator, emphasizing the criticality of refining reference signal design. Solutions might involve providing smaller sequential references or constraining maximum pitch and roll angles.

![cc](https://live.staticflickr.com/65535/53353830367_96ca0f1119_c.jpg)

These observations underscored the need for controller fine-tuning to enhance reference tracking performance. Designing a controller capable of concurrently tracking all four references without excessive oscillations and with rapid response times emerged as a significant challenge.

In conclusion, the developed models exhibited accuracy and reliability in predicting real drone behaviour. Furthermore, they serve as a foundation for designing more effective controllers to optimize drone performance.

{{< hint tip >}}
[Continuous Controller Design](https://ricardochin.com/docs/2code/1drone/_index3/){{< /hint >}}