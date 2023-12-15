---
title: Drone Continuous Controller
weight: 3
---

# **Phase 2. Controller Design Pt. 1**

{{< hint tip >}}
If you wish to skip the theoretical and implementation details, [please click here to jump to the summarized findings Ch. 9 - Piloting in a Loop](https://ricardochin.com/docs/code/uav/continuous-controller-design/#9-piloting-in-a-loop). I have cool images! ✌️{{< /hint >}}


## 1 Continuing from Section 1...

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

For the drone in question, only the position (Px, Py, and Pz) and orientation ({{< katex >}}\phi, \theta, \text{and} \psi{{< /katex >}}) are directly measurable. Consequently, the estimation of linear velocity ({{< katex >}}\dot{P}_x, \dot{P}_y \text{and} \dot{P}_z {{< /katex >}}) and angular velocity ( {{< katex >}}\dot{\phi}, \dot{\theta} \text{and} \dot{\psi} {{< /katex >}} ) becomes necessary.

Additionally, it was indicated that sensor noise would impact the measured signals, with the noise variance specified for each sensor.

{{< details "Parameters - (click to expand)" close >}}
| Parameter               | Value               |
|-------------------------|---------------------|
| Inertial position x, px | 7.3221 × 10^-4      |
| Inertial position y, py | 4.2829 × 10^-4      |
| Inertial position z, pz | 1.8000 × 10^-4      |
| Roll, φ                 | 3.4817 × 10^-4      |
| Pitch, θ                | 2.6060 × 10^-4      |
| Yaw, ψ                  | 3.8313 × 10^-5      |
{{< /details >}}

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
Matrix P is the solution of the [algebraic Riccati equation](https://en.wikipedia.org/wiki/Algebraic_Riccati_equation).
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


## 6 Kalman-Bucy Observer

It was highlighted earlier that the system's onboard sensors aren't ideal and will inevitably introduce noise into the measurements. This noise can significantly impact the system's performance, potentially leading to errors when following a reference and even instability in the drone's motion, particularly in the more sensitive nonlinear system, susceptible to actuation oscillations.

There are two distinct noise types—process noise (v(t)) and sensor noise (w(t)), both assumed to be zero-mean white noise with known variance.

Due to the presence of noise in the measured values, the accuracy of the implemented Luenberger observer in estimating the state space variables diminishes. To counteract this, the Kalman-Bucy observer will be utilized.

The process of tuning the Kalman filter gains in matrix Lk involves adjusting Q0 and R0 values. The known variance values from the sensor noise were allocated to R0.

Q0 values were determined via a trial-and-error approach until the noise impact was minimized. The resulting values for the yaw, vertical, longitudinal, and lateral subsystems are articulated in equations:

{{< katex display >}}
Q_0^\text{yaw} = 10^{-6}
{{< /katex >}}
{{< katex display >}}
Q_0^\text{vertical} = 10^{-6}
{{< /katex >}}
{{< katex display >}}
Q_0^\text{longitudinal} = 10^{-10}
{{< /katex >}}
{{< katex display >}}
Q_0^\text{lateral} = 10^{-10}
{{< /katex >}}

Combining the developed LQR with the Kalman-Bucy observer results the Linear Quadratic Gaussian.

![124f](https://live.staticflickr.com/65535/53354783288_ddc4e8a856_c.jpg)


It's crucial to note that, similar to the LQR, the relative values assigned to Q0 and R0 significantly impact the observer's performance. Consequently, the small values in Q0 correspond to the small values of sensor noise variances present in R0.

Additionally, it's important to acknowledge that despite my best effort, successfully eliminating the noise proved challenging. This difficulty persisted even after attempting various values for the Kalman filter parameters. The impact of these results, particularly evident in the nonlinear simulation, is thoroughly discussed in the simulation results.


## 7 Simulation Results

I conducted comprehensive testing of the implemented controllers (via pole placement and LQR) and observers (Luenberger and Kalman-Bucy) using both the linear simulator developed in the initial phase and the full nonlinear simulator provided in the subsequent phase.

{{< hint warning >}}
Two primary tests were conducted to evaluate performance:

- 1. Step Reference Test: This involved applying step changes to the four references (Px, Py, Pz, and ψ). The objective was to assess performance in a complex scenario where the controller needed to combine four actuations to track a four-degree-of-freedom motion.

- 2. Ramp Reference Test: A series of ramps was utilized to create a simpler yet realistic trajectory for the drone to follow.{{< /hint >}}

Both tests were executed in both simulators, comparing results with and without noise injected into the sensors. The Simulink block "Band-Limited White Noise" was employed by the group to implement the noise. The aim was to comprehensively evaluate the controllers' and observers' performance under various scenarios and noise conditions using different trajectory types. This allowed for a robust assessment of their effectiveness and robustness in real-world scenarios.



### 7.1. Linear Model

The used steps provide the following references to the system:


{{< details "References - (click to expand)" close >}}
| Variable | Value    |
|----------|----------
| px       | 1m       |
| py       | 1m       |
| pz       | -1m      |
| ψ        | π/8 rad  |    
{{< /details >}}

Using the pole placement controller and the Luenberger observer without noise in the measurements results:

![sdaji](https://live.staticflickr.com/65535/53353685957_eb75ae80b6_z.jpg)

Ensuring that the roll and pitch angles remain within acceptable ranges is crucial, as discussed in the initial phase. Large values of these angles could potentially lead the drone to lose all vertical propulsion forces. In the nonlinear simulator, this scenario might cause the drone to descend rapidly and crash the simulation.

Observing the following figure allowed verification that the roll and pitch angles were maintained at acceptable levels, preventing potential issues that could result in the loss of vertical propulsion forces and subsequent simulation crashes. This detail is particularly critical in maintaining stable flight conditions and avoiding adverse consequences due to extreme pitch and roll angles.


{{< details "Pitch and Roll via using Pole placement Controller and Luenberger observer - (click to expand)" close >}}
![148](https://live.staticflickr.com/65535/53354783263_4ea82c73d8_c.jpg)
{{< /details >}}


Verifying that the actuation did not saturate:

{{< details "Actuation of each motor using the Pole placement Controller and Luenberger observer - (click to expand)" close >}}
![148](https://live.staticflickr.com/65535/53355010410_b1e5e8371b.jpg)
{{< /details >}}

In the subsequent test, similar to the previous one, the LQR controller was employed instead of the pole placement controller. However, the observer used remained the Luenberger observer, as no noise was introduced to the measurements. 

{{< details "Response of the states following the step values with LQR and a Luenberger observer - (click to expand)" close >}}
![148222](https://live.staticflickr.com/65535/53355010400_8694f26456_w.jpg)
{{< /details >}}

Verifying again that the pitch and roll angles stay within reasonable values:

{{< details "Pitch and Roll using the LQR formula and Luenberger observer - (click to expand)" close >}}
![14118222](https://live.staticflickr.com/65535/53355010395_98a2c89a8a_z.jpg)
{{< /details >}}

{{< details "Actuation for the LQR and Luenberger observer - (click to expand)" close >}}
![141182222222](https://live.staticflickr.com/65535/53353685947_3bc65c694b_w.jpg)
{{< /details >}}

{{< hint info >}}
Observing the actuation, it's apparent that not only does the actuation avoid reaching saturation values, but it also remains lower compared to the values attained using the pole placement controller. This outcome was anticipated since the LQR operates as an optimal controller, balancing system performance with energy consumption.{{< /hint >}}

Moving forward, the analysis focuses on the system's behaviour when noise affects the measurements provided by the sensors. As previously discussed, the Luenberger estimator generates estimations with errors and becomes impractical when noise corrupts the measurements. To address this challenge, the developed Kalman-Bucy observer is utilized. T

{{< hint info >}}
Kalman filter effectively reduces most of the noise from all measured signals, while the Luenberger estimator follows the corrupted signal. This emphasizes the superiority of the Kalman-Bucy observer in mitigating noise-induced errors compared to the Luenberger estimator.{{< /hint >}}

The sensors and noise estimations can be evaluated:

![141182211122222](https://live.staticflickr.com/65535/53355010365_a751883e22_z.jpg)

Now, performing the identical test where the four steps serve as references, yet employing the LQR with the Kalman-Bucy observer (Linear Quadratic Gaussian), the results depicted as follows confirm our expectations. The LQG successfully tracks the references even in the presence of noise in the measurements. This reaffirms the robustness and effectiveness of the LQG controller coupled with the Kalman-Bucy observer in handling noisy measurements while accurately following the given references.

{{< details "Step for LQG - (click to expand)" close >}}
![14118222222222](https://live.staticflickr.com/65535/53354560101_d37d9bdba3_w.jpg)
{{< /details >}}

{{< details "Pitch and Roll for LQG - (click to expand)" close >}}
![141182221112222](https://live.staticflickr.com/65535/53354560096_71c25fd1c2.jpg)
{{< /details >}}

{{< details "Actuation for LQG - (click to expand)" close >}}
![141182aa221112222](https://live.staticflickr.com/65535/53355010355_478e46a2d6.jpg)
{{< /details >}}

### 7.2. Nonlinear Model

Using the same steps of the previous section and using the pole placement controller with the Luenberger
observer:

{{< details " Actuation of each motor using the pole placemente controller and a Luenberger observer - (click to expand)" close >}}
![14118zzz222222222](https://live.staticflickr.com/65535/53354560091_b9b014848e.jpg)
{{< /details >}}

{{< details "Pitch and Roll using the pole placement controller and Luenberger observer - (click to expand)" close >}}
**Roll and pitch angles are acceptable.**
![14118222111zzz2222](https://live.staticflickr.com/65535/53354886429_a819b44650_z.jpg)
{{< /details >}}

{{< details "Actuation of each motor using the pole placement controller and the Luenberger observer - (click to expand)" close >}}
**Actuation did not saturate.**
![17](https://live.staticflickr.com/65535/53354783198_552bc665a9_w.jpg)
{{< /details >}}


Using now the LQR with the Luenberger estimator, the reference tracking results:

{{< details "Position tracking with LQR and Luenberger observer - (click to expand)" close >}}
![18](https://live.staticflickr.com/65535/53354560061_17d450e244_w.jpg)
{{< /details >}}

{{< details "Pitch and Roll using the LQR and Luenberger observer - (click to expand)" close >}}
**Roll and pitch angles are acceptable.**
![19](https://live.staticflickr.com/65535/53354783178_ffe9496151_z.jpg)
{{< /details >}}

{{< details "Actuation of LQR and Luenberger observer - (click to expand)" close >}}
**Actuation did not saturate.**
![20](https://live.staticflickr.com/65535/53355010280_3f2ba98efb_w.jpg)
{{< /details >}}

Having shown in the previous section that when noise is present, the Kalman-Bucy observer is used, the same test using the Linear Quadratic Gaussian will be assessed.

{{< details "Step with LQG - (click to expand)" close >}}
![21](https://live.staticflickr.com/65535/53354560061_17d450e244_w.jpg)
{{< /details >}}

{{< details "Pitch and Roll with LQG - (click to expand)" close >}}
**Again, the roll and pitch angles are reasonable. Notice that the absolute maximum value for both pitch and roll is around 0.03 rad (1.71 degrees)**.
![22](https://live.staticflickr.com/65535/53354886379_2da349c458.jpg)
{{< /details >}}

{{< details "Actuation with LQR and Kalman-Bucy - (click to expand)" close >}}
**Actuation did not saturate.**
![23](https://live.staticflickr.com/65535/53354886364_a4384d9baf.jpg)
{{< /details >}}

### 7.3. Remarks

From the comprehensive results obtained from both linear and nonlinear models, it can be concluded that all controllers and observers were correctly implemented and exhibited their intended functionality.

An essential finding across both models was the significance of using the Kalman-Bucy observer when noise significantly impacts sensor readings. This observer proved crucial in handling noisy measurements effectively.

Careful consideration of roll and pitch angles was emphasized due to their potential impact on causing rapid altitude loss in the nonlinear simulation. Numerous instability issues were encountered, especially when step values were too large, resulting in aggressive actuation and subsequent system instability. To mitigate these issues, alternative reference signals were introduced to reduce initial error and consequent motor actuation.

{{< hint note >}}
Addressing actuation saturation and avoiding extreme pitch and roll angles can be achieved through various methods. One such solution involves implementing a saturation block on motors to ensure voltages stay within permissible limits. However, a straightforward saturation approach on individual motors may hinder the drone from following the desired trajectory. To resolve this, a strategy of limiting all actuations whenever one motor saturates allows the drone to continually attempt trajectory following without losing control or erratic behavior. This approach ensures a more controlled response in scenarios where actuation saturation or extreme angles could destabilize the system.
{{< /hint >}}

## 8 Trajectory Following


After successfully testing the system with step references, I implemented a trajectory using a series of ramps to guide the drone's trajectory, aiming to minimize initial error. This trajectory, designed to trace a "8" shape with the drone landing at its initial take-off position, underwent testing in both the linear and nonlinear models.

To determine the maximum slope of the reference ramps, a trial-and-error process was conducted using the nonlinear model. By gradually increasing the slopes of the ramps (for the four references) until the drone became uncontrollable. Moreover, it was noted that the maximum pitch and roll angles remained below 0.6 radians (approximately 35 degrees).

Similar to the steps test, I was interested in evaluating trajectory tracking accuracy, assessing steep roll and pitch angles, and identifying any actuation saturation during the ramp trajectory test. Considering previous conclusions, I opted to use the Linear Quadratic Regulator without noise and the Linear Quadratic Gaussian when noise was introduced to the sensors. This choice allows for a comprehensive evaluation of trajectory tracking performance and system stability under different conditions, ensuring the suitability of the controllers and observers for both noise-free and noisy sensor scenarios.

### 8.1. Linear Model

As it can be seen for the reference tracking of the drone, the results are satisfactory and the control system was able to follow the entire trajectory with no issues.

{{< details "Reference and response along the trajectory - (click to expand)" close >}}
![24](https://live.staticflickr.com/65535/53354783173_1ff98e2030.jpg)
{{< /details >}}

{{< details "Actuation of each motor along the trajectory of the linear system - (click to expand)" close >}}
**Actuation did not saturate.**
![25](https://live.staticflickr.com/65535/53355010245_9c43b76a48.jpg)
{{< /details >}}

{{< details "Response of the angles along the trajectory of the Linear system - (click to expand)" close >}}
**The roll and pitch angles are reasonable.**
![26](https://live.staticflickr.com/65535/53354560006_f275d2014e_b.jpg)
{{< /details >}}

{{< hint example >}}
I also created a 3D plot of the trajectory flown by the drone for the linear system. Plot is seen in the following figure:
{{< /hint  >}}
![27](https://live.staticflickr.com/65535/53354783148_4bacc67c53.jpg)



{{< hint warning >}}
It was previously established that when sensor noise is present, employing the Kalman-Bucy observer becomes essential. The next figure demonstrates that without the Kalman filter, the drone's motion becomes erratic, showcasing evident actuation saturation issues.{{< /hint >}}
![28](https://live.staticflickr.com/65535/53354560001_a8133de54e.jpg)

Logically, adding the **Kalman filter to tackle the noise**, the results improve.

{{< details "Reference and response along the trajectory - (click to expand)" close >}}
![29](https://live.staticflickr.com/65535/53355010200_e435b25f4c.jpg)
{{< /details >}}

{{< details "Actuation of each motor along the trajectory - (click to expand)" close >}}
**Actuation did not saturate.**
![30](https://live.staticflickr.com/65535/53355010205_5774ce6dcb.jpg)
{{< /details >}}

{{< details "Pitch and roll along the trajectory - (click to expand)" close >}}
**The roll and pitch angles are reasonable.**
![31](https://live.staticflickr.com/65535/53354559936_a12198f8db_b.jpg)
{{< /details >}}

{{< hint example >}}
Aside from the 3D plot of the trajectory, I showcase a zoomed section showcasing the bits of oscillation due to noise.
{{< /hint  >}}
![32](https://live.staticflickr.com/65535/53355010185_6f80089731.jpg)
![33](https://live.staticflickr.com/65535/53353685792_24fe13afc8.jpg)

### 8.2. Nonlinear Model

Following a similar strategy as in ch. 8.1. Linear Model:

{{< details "Reference and response along the trajectory - (click to expand)" close >}}
![34](https://live.staticflickr.com/65535/53353685802_75ba574739.jpg)
{{< /details >}}

{{< details "Actuation of each motor along the trajectory - (click to expand)" close >}}
**Actuation did not saturate.**
![35](https://live.staticflickr.com/65535/53354886279_ff1b645f25.jpg)
{{< /details >}}

{{< details "Response of the angles along the trajectory - (click to expand)" close >}}
**The roll and pitch angles are reasonable.**
![36](https://live.staticflickr.com/65535/53353685737_79f9b9e039_b.jpg)
{{< /details >}}

{{< hint example >}}
I also created a 3D plot of the trajectory flown by the drone for the nonlinear system. Plot is seen in the following figure:
{{< /hint  >}}
![37](https://live.staticflickr.com/65535/53354886229_44640d6c05.jpg)

Adding noise to the sensor measurements and using the Kalman-Bucy observer, the following results are obtained:

{{< details "Reference and response along the trajectory - (click to expand)" close >}}
![38](https://live.staticflickr.com/65535/53354559896_a219466c40.jpg)
{{< /details >}}

{{< details "Actuation of each motor along the trajectory - (click to expand)" close >}}
![39](https://live.staticflickr.com/65535/53353685722_5e81cf1258.jpg)
{{< /details >}}

{{< details "Pitch and roll along the trajectory - (click to expand)" close >}}
![40](https://live.staticflickr.com/65535/53354783053_7c1cf5445c_z.jpg)
{{< /details >}}


{{< hint example >}}
Aside from the 3D plot of the trajectory, I showcase a zoomed section showcasing the bits of oscillation due to noise.
{{< /hint  >}}
![41](https://live.staticflickr.com/65535/53354559871_14bcea007f_w.jpg)
![42](https://live.staticflickr.com/65535/53355010105_5f9abfb1ed_w.jpg)

## 9 Piloting in a Loop

This work section aimed to design and test controllers and observers crucial for drone control.

Initially, by designing a controller using the pole placement method, employing Ackermann’s Formula. Simultaneously, a Luenberger observer was tailored to estimate variables unmeasurable by physical means, aligning its dynamics to be faster than the controller.

![aa](https://live.staticflickr.com/65535/53355021829_e45c28a340_c.jpg)

Subsequently, an optimal controller and observer were developed, specifically the Linear Quadratic Regulator (LQR) and the Kalman-Bucy observer. The LQR demonstrated efficient reference tracking with reduced actuation, minimizing energy consumption. However, with the Kalman filter, despite attempts to tune it, complete noise removal wasn't achieved, especially in the sensitive nonlinear simulator, affecting responses to step references.

![bb](https://live.staticflickr.com/65535/53355145110_1647537978_c.jpg)

Nonetheless, the Kalman filter proved beneficial in noise-affected sensor scenarios, although further tuning is intended to improve its performance.

In addition, it was also introduced a solution using refined reference signals, enhancing trajectory tracking and stability compared to step references. 

![cc](https://live.staticflickr.com/65535/53355021834_2f6c597ac8_c.jpg)

{{< hint tip >}}
[Advance to Discrete Controller Design Here ✌️](https://ricardochin.com/docs/code/uav/discrete-controller-design/){{< /hint >}}
