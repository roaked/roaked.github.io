---
title: Drone Controller Design Pt. 1 
weight: 3
---

# **Controller Design Pt. 1**

## 1 Continuing...

Previously, I had addressed problem formulation and initial approaches. The primary tasks included studying the system and subsystems, creating a state-space model for the dynamical system, analyzing aspects like linearity, stability, controllability, and observability, developing Simulink models, proposing classical state feedback control, and validating the simulator for the developed models.

{{< hint info >}}
Following the delivery of Phase I, issues were identified in the drone controller. Although functional, some minor problems remained unresolved. The movement subsystem had initially been modeled as a 12th order system, established as linearizable, controllable, and observable. In the upcoming work, the decision was made to break down the movement system into four subsystems: vertical, lateral, longitudinal axis movement, and yaw subsystem.
{{< /hint >}}

The primary objective is to formulate and address optimal control and estimation problems. It is intended to compare outcomes from the pole placement controller and the Luenberger observer with those achieved using the optimal controller (Linear Quadratic Regulator) and optimal observer (Kalman-Bucy observer).

## 2 Actuation and Sensors

Before commencing the formulation and implementation of the required controllers and estimators, there are preliminary details that necessitate consideration.

One of the initial considerations revolves around the actuation, specifically the voltages applied to each motor. It was established during the project's initial phase that the model was linearized under a hovering condition. From this state, the computation of the required equilibrium motor actuation was derived.

{{< katex display >}}
V_0 = 7.9V
{{< /katex >}}


In the context of the linear model, a null actuation maintains the system in a hovering state, implying no resultant motion. This characteristic arises from the actuation being a deviation from the nominal value rather than the actual voltage applied to the motors. To prevent motor saturation, it's crucial to adhere to the following voltage deviation constraints:

{{< katex display >}}
\Delta V_{\text{max}} = V_{\text{bat}} - V_0 = 11.1 - 7.9 = 3.2 \, \text{V}
{{< /katex >}}
{{< katex display >}}
\Delta V_{\text{min}} = V_0 = 7.9 \, \text{V}
{{< /katex >}}

In the non-linear model, maintaining the hovering condition necessitates applying V0 to all motors. However, the controllers are formulated based on the linear model. Therefore, the voltage offset should only be added after the controllers compute the necessary actuation. As a result, the maximum and minimum voltage deviations align with those of the linear model. Another crucial aspect involves the design of observers. Understanding which state variables lack direct physical measurement is essential, as these variables demand estimation using an observer.


![147](https://live.staticflickr.com/65535/53354560156_b9fd30fa8b_z.jpg)

For the drone in question, only the position (Px, Py, and Pz) and orientation ({{< katex >}}\phi, \theta, \text{and} \psi{{< /katex >}}) are directly measurable. Consequently, the estimation of linear velocity ({{< katex >}}\dot{P}_x, \dot{P}_y \text{and} \dot{P}_z {{< /katex >}}) and angular velocity ({{< katex >}}\dot{\phi}, \dot{\theta} \text{and} \dot{\psi} \dot {{< /katex >}}) becomes necessary.

Additionally, it was indicated that sensor noise would impact the measured signals, with the noise variance specified for each sensor.

| Parameter               | Value               |
|-------------------------|---------------------|
| Inertial position x, px | 7.3221 × 10^-4      |
| Inertial position y, py | 4.2829 × 10^-4      |
| Inertial position z, pz | 1.8000 × 10^-4      |
| Roll, φ                 | 3.4817 × 10^-4      |
| Pitch, θ                | 2.6060 × 10^-4      |
| Yaw, ψ                  | 3.8313 × 10^-5      |

It was suggested that the noise issue could potentially be addressed by employing the Kalman-Bucy estimator in place of the Luenberger observer.

## 3 Servo Feedback Control Via Pole Placement

The objective of stabilizing the system while enabling reference tracking necessitates the implementation of a servo feedback control system. This entails designing the matrices K and ki, as depicted:

![2138](https://live.staticflickr.com/65535/53355010435_d9cfc7a3d2_b.jpg)


Before computing the controller parameters, the selection of desired poles becomes crucial for modifying the system dynamics. As outlined previously, the model can be separated into four distinct subsystems, each with poles located at the origin. Furthermore, it was demonstrated that all these subsystems are controllable.

Specifically, the vertical axis and yaw subsystems are both represented as second-order systems and follow the structure defined in equation 4.

{{< katex display >}}
\begin{bmatrix}
\dot{x}_1(t) \\
\dot{x}_2(t) \\
\end{bmatrix}
= \begin{bmatrix}
0 & 0 \\
1 & 0 \\
\end{bmatrix}
\begin{bmatrix}
x_1(t) \\
x_2(t) \\
\end{bmatrix}
+
\begin{bmatrix}
b \\
0 \\
\end{bmatrix}
u(t) \\
{{< /katex >}}

{{< katex display >}}
y(t) = x_2(t) = [0 \, 1] \begin{vmatrix}
x_1(t) \\
x_2(t) \\
\end{bmatrix}
{{< /katex >}}

The augmented system is, therefore, given by:

{{< katex display >}}
\begin{bmatrix}
\dot{x}_1(t) \\
\dot{x}_2(t) \\
\dot{x}_i(t) \\
\end{bmatrix}
= \begin{bmatrix}
0 & 0 & 0 \\
1 & 0 & 0 \\
0 & -1 & 0 \\
\end{bmatrix}
\begin{bmatrix}
x_1(t) \\
x_2(t) \\
x_i(t) \\
\end{bmatrix}
+
\begin{bmatrix}
b \\
0 \\
0 \\
\end{bmatrix}
u(t) \\
{{< /katex >}}


{{< katex display >}}
y(t) = x_2(t) = [0 \, 1 \, 0] \begin{bmatrix}
x_1(t) \\
x_2(t) \\
x_i(t) \\
\end{bmatrix}
{{< /katex >}}

By employing Ackermann’s Formula through the [MATLAB function "acker"](https://de.mathworks.com/help/control/ref/place.html) and selecting all poles at -5, the controller gains for the vertical axis and yaw subsystems are provided by:

{{< katex display >}}
\bar{K}_{\text{vertical}} = [-24.16, -120.79, 201.32]
{{< /katex >}}

{{< katex display >}}
\bar{K}_{\text{yaw}} = [6.58, 32.90, 54.83]
{{< /katex >}}


The remaining two subsystems, the lateral and longitudinal subsystems, are composed of two second-order systems each (roll and pitch, respectively). Consequently, these subsystems are both fourth-order systems and follow the structure defined:

{{< katex display >}}
\begin{bmatrix}
\dot{x}_1(t) \\
\dot{x}_2(t) \\
\dot{x}_3(t) \\
\dot{x}_4(t)\\
\end{bmatrix}
= \begin{bmatrix}
0 & 0 & 0 & 0 \\
1 & 0 & 0 & 0 \\
0 & Q_{32} & 0 & 0 \\
0 & 0 & 1 & 0 \\
\end{bmatrix}
\begin{bmatrix}
x_1(t) \\
x_2(t) \\
x_3(t) \\
x_4(t) \\
\end{bmatrix}
+
\begin{bmatrix}
b \\
0 \\
0 \\
0 \\
\end{bmatrix}
u(t)
{{< /katex >}}

{{< katex display >}}
y(t) = x_4(t) = [0 \, 0 \, 0 \, 1] \begin{bmatrix}
x_1(t) \\
x_2(t) \\
x_3(t) \\
x_4(t)\\
\end{bmatrix}
{{< /katex >}}

The resultant augmented system is:
{{< katex display >}}
\begin{bmatrix}
\dot{x}_1(t) \\
\dot{x}_2(t) \\
\dot{x}_3(t) \\
\dot{x}_4(t) \\
\dot{x}_i(t) \\
\end{bmatrix}
= \begin{bmatrix}
0 & 0 & 0 & 0 & 0 \\
1 & 0 & 0 & 0 & 0 \\
0 & Q_{32} & 0 & 0 & 0 \\
0 & 0 & 1 & 0 & 0 \\
0 & 0 & 0 & -1 & 0 \\
\end{bmatrix}
\begin{bmatrix}
x_1(t) \\
x_2(t) \\
x_3(t) \\
x_4(t) \\
x_i(t) \\
\end{bmatrix}
+
\begin{bmatrix}
b \\
0 \\
0 \\
0 \\
0 \\
\end{bmatrix}
u(t)
{{< /katex >}}


{{< katex display >}}
y(t) = x_4(t) = [0 \, 0 \, 0 \, 1 \, 0] \begin{bmatrix}
x_1(t) \\
x_2(t) \\
x_3(t) \\
x_4(t) \\
x_i(t) \\
\end{bmatrix}
{{< /katex >}}

Through Ackermann’s Formula and after a trial-and-error process, selecting all poles at -5, the controller gains for the lateral axis and longitudinal axis subsystems are represented:
{{< katex display >}}
\bar{K}_{\text{lateral}} = [1.49, 14.90, 7.59, 18.99, -18.99]
{{< /katex >}}

{{< katex display >}}
\bar{K}_{\text{longitudinal}} = [1.69, 16.93, -8.63, -21.57, 21.57]
{{< /katex >}}

## 4 Luanberger Observer

As previously mentioned, the onboard sensors can only physically measure position and orientation. Consequently, the estimation of linear and angular velocities within the subsystems' state vectors becomes necessary.

The fundamental concept behind an estimator involves creating a numerical simulator of the physical system, receiving actuation as input, and producing the state space vector estimation as output. However, this approach alone isn't sufficient. The estimation error can asymptotically vanish if the state is unstable, and the convergence velocity of the error is limited by the system's velocity.

To address this, the implementation of a Luenberger observer is essential. As depicted in the following figure, the adjustment of observer performance can be achieved by tuning the parameters within matrix L.

![124918](https://live.staticflickr.com/65535/53355010430_806fed4271_z.jpg)

To derive the Luenberger observer, it's crucial to select the desired poles ensuring that the observer operates faster than the system dynamics without excessively amplifying modeling errors. In this scenario, for this system, a choice was made to position the poles 10 times faster than those of the controller, thus setting all poles at -50. Additionally, it's pertinent to note that, as previously explained in the initial phase, the system is observable.

Utilizing Ackermann’s formula, specifically via the [MATLAB function "acker"](https://de.mathworks.com/help/control/ref/place.html), the matrix L for the designed Luenberger observer for the vertical, yaw, lateral, and longitudinal subsystems can be described in the following equations:

{{< katex display >}}
L_{\text{vertical}} = \begin{bmatrix} 2500 \\ 100 \end{bmatrix}
{{< /katex >}}

{{< katex display >}}
L_{\text{yaw}} = \begin{bmatrix} 2500 \\ 100 \end{bmatrix}
{{< /katex >}}

{{< katex display >}}
L_{\text{lateral}} = \begin{bmatrix} 6.371 \\ 0.510 \\ 0.150 \\ 0.002 \end{bmatrix}
{{< /katex >}}

{{< katex display >}}
L_{\text{longitudinal}} = \begin{bmatrix} -6.371 \\ -0.510 \\ 0.150 \\ 0.002 \end{bmatrix}
{{< /katex >}}

## 5 Linear Quadratic Regulator 

The pole placement method demonstrated in the previous sections yielded satisfactory outcomes. However, it poses challenges in tuning the controller, becoming increasingly complex as the system's order increases. Additionally, it complicates the task of finding a compromise between conflicting objectives when designing a controller.

In light of this, the focus now shifts to considering an optimal controller that takes into account conflicting goals and strategically places poles in an optimal manner. Specifically, the design will involve a linear quadratic regulator (LQR). In designing an optimal controller, the usual objective is to minimize energy consumption (represented by the control action) while maximizing the distance traveled—objectives that clearly conflict.

The initial step involves selecting a performance index J that reflects these conflicting objectives. In this case, the performance index specified by the following equation is utilized, aiming to be minimized.

{{< katex display >}}
J = \int_{0}^{\infty} (x^T(t) Q x(t) + u^T(t) R u(t)) \, dt
{{< /katex >}}

Certainly, in a manner akin to PID controller parameters, the matrices Q (where Q ≥ 0) and R (where R > 0) play a pivotal role. They contribute to the behaviour of the system and need to be tuned to achieve the desired system response. Augmenting Q results in faster convergence of states toward the reference, while increasing R penalizes larger control inputs. The balance between performance and energy expended is thus represented by the relative values assigned to both matrices.

In an optimal controller, the controller gains aren't constant over time. However, it's more common to design a sub-optimal controller where K is a constant value. Adhering to this approach, the computation of matrix K is outlined:

{{< katex display >}}
K = R^{-1}B^T P
{{< /katex >}}

{{< hint tip >}}
Matrix P is the solution of the algebraic Riccati equation:

{{< katex display >}}
0 = A^T P + P A - PBR^{-1}B^T P + Q
{{< /katex >}}
{{< /hint >}}


It was mentioned that, in pursuit of tracking a reference rather than merely maintaining states at zero, [the MATLAB function "lqi" (linear quadratic integrator)](https://de.mathworks.com/help/control/ref/ss.lqi.html) becomes crucial as it computes both K and ki values.

The success in achieving desired performance largely hinges on the choice of relative values within Q and R matrices. Specifically, emphasis was placed on the need to tune diagonal values, allowing for certain values to be set to zero if controlling a particular state isn't deemed necessary.

Regarding the yaw angle and vertical axis subsystems, considering their augmented third-order nature, the prescribed values for Q and R were specified through equations:

{{< katex display >}}
Q_{\text{yaw}} = \begin{bmatrix}
0 & 0 & 0 \\
0 & 10 & 0 \\
0 & 0 & 50 \\
\end{bmatrix}
; \quad R_{\text{yaw}} = 1
{{< /katex >}}


{{< katex display >}}
Q_{\text{vertical}} = \begin{bmatrix}
1 & 0 & 0 \\
0 & 50 & 0 \\
0 & 0 & 100 \\
\end{bmatrix}
; \quad R_{\text{vertical}} = 1
{{< /katex >}}

In regard to the longitudinal axis and lateral axis subsystems, the augmented systems are identified as fifth-order. The designated values for Q and R are specifically detailed:

{{< katex display >}}
Q_{\text{longitudinal}} = \begin{bmatrix}
0 & 0 & 0 & 0 & 0 \\
0 & 10 & 0 & 0 & 0 \\
0 & 0 & 1 & 0 & 0 \\
0 & 0 & 0 & 10 & 0 \\
0 & 0 & 0 & 0 & 50 \\
\end{bmatrix}
; \quad R_{\text{longitudinal}} = 1
{{< /katex >}}


{{< katex display >}}
Q_{\text{longitudinal}} = \begin{bmatrix}
0 & 0 & 0 & 0 & 0 \\
0 & 10 & 0 & 0 & 0 \\
0 & 0 & 1 & 0 & 0 \\
0 & 0 & 0 & 10 & 0 \\
0 & 0 & 0 & 0 & 50 \\
\end{bmatrix}
; \quad R_{\text{longitudinal}} = 1
{{< /katex >}}