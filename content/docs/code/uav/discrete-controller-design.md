---
title: Drone Discrete Controller
weight: 4
---

# **Phase 3. Amazing Race & Crazy 8**

{{< hint tip >}}
If you wish to skip the theoretical and implementation details, [please click here to jump to the summarized findings Ch. 8 - Amazing Race](https://ricardochin.com/docs/code/uav/discrete-controller-design/#8-amazing-race). I have cool images! ✌️{{< /hint >}}

## 1 Continuing from Phase 2...

In this phase 3, a more practical approach was adopted to tackle the problem. Discrete controllers and observers were designed for the drone, with simulations implemented to test it in a highly realistic environment. This involved using provided tracks and fine-tuning controllers to minimize following errors while maintaining optimal speed without causing critical destabilization.

## 2 Discrete Time Linearized Model

As previously indicated, the drone model can be segmented into four subsystems. Among these, the yaw and vertical axis subsystems are second-order.


{{< katex display >}}
\dot{X}(t) =
\begin{bmatrix}
0 & 0 \\
1 & 0 \\
\end{bmatrix}
X(t) +
\begin{bmatrix}
b \\
0 \\
\end{bmatrix}
u(t)
{{< /katex >}}

The longitudinal and lateral axis subsystems are fourth order.

{{< katex display >}}
\dot{X}(t) =
\begin{bmatrix}
0 & 0 & 0 & 0 \\
1 & 0 & 0 & 0 \\
0 & g & 0 & 0 \\
0 & 0 & 1 & 0 \\
\end{bmatrix}
X(t) +
\begin{bmatrix}
b \\
0 \\
0 \\
0 \\
\end{bmatrix}
u(t)
{{< /katex >}}

The discretization of a continuous time state space model only affects the A and B matrices. The resultant
discretized A and B matrices are computed as shown in the following equations:  

{{< katex display >}}
\bar{A} = e^{A^T} = \mathcal{L}^{-1}(sI - A)^{-1}
{{< /katex >}}
{{< katex display >}}
\bar{B} = \int_{0}^{T} e^{A\tau} B \, d\tau \quad \text{T : Sample Time}
{{< /katex >}}

The discretized second order state space models are, therefore, given:

{{< katex display >}}
X(k + 1) = \begin{bmatrix}
1 & 0 \\
T & 1 \\
\end{bmatrix} X(k) + \begin{bmatrix}
bT \\
b \frac{T^2}{2} \\
\end{bmatrix} u(k)
{{< /katex >}}

The discretized fourth order state space models are:

{{< katex display >}}
X(k + 1) = \begin{bmatrix}
1 & 0 & 0 & 0 \\
T & 1 & 0 & 0 \\
g T^2/2 & gT & 1 & 0 \\
gT^3/6 & gT^2/2 & T & 1 \\
\end{bmatrix} X(k) + \begin{bmatrix}
bT \\
bT^2/2 \\
bgT^3/6 \\
bgT^4/24 \\
\end{bmatrix} u(k)
{{< /katex >}}

To derive the discrete models mentioned, the [c2d command was utilized in MATLAB](https://de.mathworks.com/help/ident/ref/dynamicsystem.c2d.html). This command was applied to the state-space model generated via [the ss command](https://de.mathworks.com/help/control/ref/ss.html). A zero-order hold was implemented at the input of the continuous system, and a sample time of 0.01s was assumed. Computing the poles of the obtained discrete models can be obtained:

{{< katex display >}}
\left|zI - \bar{A}\right| = 0
{{< /katex >}}

![1](https://live.staticflickr.com/65535/53355765118_b7b9974302_b.jpg)

This figure displays the root locus concerning the second and fourth-order discrete systems. As anticipated, all poles are positioned at z = 1, aligning with the system's marginal stability where its poles were situated at s = 0 in continuous time.

In earlier project sections, the system underwent analysis regarding its controllability and observability. With the current focus on a discrete model, the evaluation extends to its reachability — confirming the system's capability to transition from any initial state to any final state through a finite actuation sequence. Additionally, observability is scrutinized to determine if it's feasible to ascertain the initial state from observing the system output within a specified time interval. 

Two other pertinent properties are controlability, assured if reachability is established, and reconstructability, ensured if observability is confirmed. These aspects collectively contribute to understanding and manipulating the system in its discrete form. To assess the reachability of the system, it's essential to calculate the rank of the reachability matrix.


{{< katex display >}}
\text{rank} \left(\bar{B} \bar{A} \bar{B} \ldots \bar{A}^{n-2} \bar{B} \bar{A}^{n-1} \bar{B}\right) = n
{{< /katex >}}

For the second and fourth order systems, the rank of the reachability matrix is given by:

{{< katex display >}}
\text{rank} \begin{bmatrix}
bT & bT \\
b\frac{T^2}{2} & 3b\frac{T^2}{2} \\
\end{bmatrix} = 2 \quad \forall T > 0

{{< /katex >}}

{{< katex display >}}
\text{rank} \begin{bmatrix}
bT & bT & bt & bt \\
b\frac{T^2}{2} & 7b\frac{T^2}{2} & 5b\frac{T^2}{2} & 3b\frac{T^2}{2} \\
bg\frac{T^3}{6} & 37bg\frac{T^3}{6} & 19bg\frac{T^3}{6} & 7bg\frac{T^3}{6} \\
bg\frac{T^4}{24} & 175b\frac{T^4}{24} & 65b\frac{T^4}{24} & 5b\frac{T^4}{8} \\
\end{bmatrix} = 4 \quad \forall T > 0
{{< /katex >}}

**Reachibility** ✔️


To determine the observability of the system, it's crucial to compute the rank of the observability matrix:

{{< katex display >}}
\text{rank} \begin{bmatrix}
\bar{C} \\
\bar{C}\bar{A} \\
\bar{C}\bar{A}^2 \\
\ldots \\
\bar{C}\bar{A}^{n-1} \\
\end{bmatrix}
{{< /katex >}}

For the second and fourth order systems, the rank of the observability matrix is given by:

{{< katex display >}}
\text{rank} \begin{bmatrix}
0 & 1 \\
T & 1 \\
\end{bmatrix} = 2 \quad \forall T > 0
{{< /katex >}}

{{< katex display >}}
\text{rank} \begin{bmatrix}
0 & 0 & 0 & 1 \\
gT^3/6 & gT^2/2 & T & 1 \\
4gT^3/3 & 2gT^2 & 2T & 1 \\
9gT^3/2 & 9gT^2/2 & 3T & 1 \\
\end{bmatrix} = 4 \quad \forall T > 0
{{< /katex >}}

**Observability** ✔️

## 3 Controller Design

With the verification of reachability and observability properties, the stage is set for designing the necessary controllers and observers. Notably, based on preceding project findings, I opted against designing a controller via pole placement. I will explore two strategies: the discrete linear quadratic regulator (LQR) and the model predictive control (MPC), both extensively discussed and analyzed in subsequent sections.

{{< hint info >}}
It's worth noting that these two controllers employ slightly different design approaches. The `LQR` is specifically tailored for each of the subsystems outlined in the previous section, mirroring the approach taken with the continuous system. This direct strategy aligns with the existence of four independent subsystems and their corresponding references, essential for the drone's tracking, considering that the roll and pitch variables are not directly controlled.

In contrast, the `MPC` considers the entire 12th order discretized system. The group's design parameters necessitated the inclusion of roll and pitch variables in the system output for better drone stabilization. While this could have been achieved by modifying the previously presented subsystems, the [MPC MATLAB toolbox's](https://www.mathworks.com/products/model-predictive-control.html) graphical interface offered a more intuitive and convenient method to design the MPC for the entire system. 
{{< /hint >}}

### 3.1. Linear Quadratic Regulator

[In the preceding phase](https://ricardochin.com/docs/code/uav/continuous-controller-design/#5-linear-quadratic-regulator), I presented and executed the LQR in the continuous domain. Now, in this phase of the project, the discrete iteration of the LQR was implemented. The LQR inherently seeks an optimal controller capable of striking a balance between performance and minimizing actuation. It achieves this by aiming to minimize the discrete version of the performance index.

{{< katex display >}}
J = \int_{0}^{\infty} (x^T(t)Qx(t) + u^T(t)Ru(t)) \, dt
{{< /katex >}}


Similar to the [approach in the continuous domain](https://ricardochin.com/docs/code/uav/continuous-controller-design/#5-linear-quadratic-regulator), matrix Q penalizes state deviations from zero, while matrix R penalizes significant actuations. However, previously, the LQR values were primarily optimized for step responses, whereas, in this phase, the optimization targeted trajectory following, commencing with the straightforward square trajectory. Employing a trial-and-error method, the following values were selectedfor each subsystem:

{{< katex display >}}
Q_{\text{vertical}} = \begin{bmatrix}
0 & 0 & 0 \\
0 & 100 & 0 \\
0 & 0 & 0.01 \\
\end{bmatrix} \quad R_{\text{vertical}} 
{{< /katex >}}

{{< katex display >}}
Q_{\text{yaw}} = \begin{bmatrix}
0 & 0 & 0 \\
0 & 10 & 0 \\
0 & 0 & 0.01 \\
\end{bmatrix} \quad R_{\text{yaw}} 
{{< /katex >}}

{{< katex display >}}
Q_{\text{longitudinal}} = \begin{bmatrix}
0 & 0 & 0 & 0 & 0 \\
0 & 0.1 & 0 & 0 & 0 \\
0 & 0 & 0.1 & 0 & 0 \\
0 & 0 & 0 & 100 & 0 \\
0 & 0 & 0 & 0 & 1 \\
\end{bmatrix} \quad R_{\text{longitudinal}}
{{< /katex >}}

{{< katex display >}}
Q_{\text{lateral}} = \begin{bmatrix}
0 & 0 & 0 & 0 & 0 \\
0 & 0.1 & 0 & 0 & 0 \\
0 & 0 & 0.1 & 0 & 0 \\
0 & 0 & 0 & 100 & 0 \\
0 & 0 & 0 & 0 & 1 \\
\end{bmatrix} \quad R_{\text{lateral}}
{{< /katex >}}

### 3.2. Model Predictive Control

The model predictive control (MPC) operates similarly to the LQR, employing an optimization approach for controller derivation. However, MPC integrates constraints within the optimization problem, addressing limitations concerning both actuation and state variables, including their respective rates of change.

In addition to imposed variable restrictions, MPC involves tuning two key parameters: the prediction horizon (`Hp`), dictating how far into the future the output is forecasted, and the control horizon (`Hc`), determining the prediction span for control actions. When both horizons tend to infinity, MPC's behavior aligns with what would be achieved using an LQR.

Concerning Hp tuning, a larger time interval generally enhances performance, but this can compromise MPC's efficacy, particularly with smaller sample times. With a 0.01s sample time, performance issues were found. To align `Hp` closer to the system's settling time, the computation time needed significantly exceeded the physical time available, rendering it impractical for real-time application. Hence, it was opted to reduce the MPC's sample time to 0.1s, enabling real-time execution while maintaining good control performance.

`Hp` tuning significantly impacts MPC stability; it should be smaller than `Hc` for stability. Beyond this requirement, its value is determined through a trial-and-error process for optimal control results.

Concerning actuation restrictions, the MPC considered physical limitations in the motors, as represented:

{{< katex display >}}
0 < u < 11.1 \text{(V)}
{{< /katex >}}

{{< hint warning >}}
Throughout this project, a prominent factor contributing to the unstable behavior of the nonlinear model was the significant tilt angle (pitch and roll) exhibited by the drone. Consequently, these restrictions were enforced within the MPC.

`−0.2 < pitch < 0.2`   ✔️
`−0.2 < roll < 0.2`   ✔️

{{< /hint >}}

The weight allocated to each reference tracking error underwent a trial-and-error tuning process. It's crucial to note that the highest weight was assigned to the vertical reference to prevent substantial altitude loss in the nonlinear model during lateral movements. This strategic allocation aimed to maintain stability and minimize altitude fluctuations during such maneuvers.

## 4 Kalman Filter Design

In the ongoing phases of this project, it's acknowledged that real-world measurements are inevitably corrupted by noise. Specifically for the drone, six independent measurements are assumed to be affected by white noise with known variances. As these noisy measurements can impact the drone's performance, selecting an estimator to mitigate these effects becomes imperative. Consequently, I employed the Kalman filter for this purpose.

{{< hint info >}}
Notably, in this phase of the project, where the primary focus lies in achieving optimal results within more realistic scenarios, the discrete Luenberger observer was disregarded. 
{{< /hint >}}


{{< details "Noise variances for Kalman Filter - (click to expand)" close >}}
| Variable | Noise Variance |
|----------|----------------|
| Px       | 7.3221e-4      |
| Py       | 4.2829e-4      |
| Pz       | 1.8000e-4      |
| Roll     | 3.4817e-4      |
| Pitch    | 2.6060e-4      |
| Yaw      | 3.8313e-5      |
{{< /details >}}

The matrix {{< katex >}}Q_0{{< /katex >}} is a pivotal parameter in gauging the confidence in the model. Following a tuning process, the value below was selected.

{{< katex display >}}
Q_0 = I_{12} \times 10^{-7}
{{< /katex >}}

The matrix {{< katex >}}R_0{{< /katex >}} is related to how reliable are the measurements and contains the noise variances.

{{< katex display >}}
R_0 = \begin{bmatrix}
7.32 \times 10^{-4} & 0 & 0 & 0 & 0 & 0 \\
0 & 4.28 \times 10^{-4} & 0 & 0 & 0 & 0 \\
0 & 0 & 1.80 \times 10^{-4} & 0 & 0 & 0 \\
0 & 0 & 0 & 3.48 \times 10^{-4} & 0 & 0 \\
0 & 0 & 0 & 0 & 2.60 \times 10^{-4} & 0 \\
0 & 0 & 0 & 0 & 0 & 3.83 \times 10^{-4} \\
\end{bmatrix}
{{< /katex >}}

## 5 Simulation

To evaluate the designed controllers, three distinct trajectories were selected: **the simple square**, **the crazy 8**, and **the amazing race** trajectories. While the waypoints forming these trajectories were provided, details such as the time instances for the drone to traverse each waypoint and the specific path geometry connecting these waypoints were left as design parameters to determine.

It is hoped to outline the methodology adopted to derive the ultimate trajectories implemented in the simulator. It is also shown the attained results and remarks on the drone's behaviour throughout these trajectories, shedding light on its performance under different control schemes.

### 5.1. Implementation


Two Simulink files — one for the LQR and another for the MPC — were created. The LQR implementation utilized relevant blocks, while the MPC and Kalman filter were integrated using available Simulink blocks. 

Efforts were made to ensure the simulator closely mimicked a real system. Consequently, both the linear and nonlinear models are continuous, while the LQR, MPC, and Kalman filter operate in discrete time. However, in practice, signals processed by discrete elements in a computer would consist of sampled points rather than the "stair-like" signals simulated using the zero-order hold.

{{< hint warning >}}
Despite attempts to replicate this behaviour using a sampler block, the Simulink solver continued to produce "stair-like" signals. Nonetheless, this discrepancy does not invalidate the results obtained, as the discrete blocks only consider signals at each multiple of the sample time. This effect is likely related to limitations within the solver.
{{< /hint >}}

Another crucial consideration is that the linearized model assumes a zero yaw angle. As the controllers were developed based on this assumption, the nonlinear model encounters issues when the yaw is not zero. To address this, a rotation about the z-axis must be applied to the output of the nonlinear model, as depicted in equation 24. This adjustment allows for the use of the same linear controllers, yielding satisfactory results even when the yaw is not zero in the nonlinear model.

{{< katex display >}}
\dot{p} = \begin{bmatrix}
\cos(\psi) & -\sin(\psi) & 0 \\
\sin(\psi) & \cos(\psi) & 0 \\
0 & 0 & 1 \\
\end{bmatrix} v
{{< /katex >}}

### 5.2. Trajectories

The connection between provided waypoints requires a specified time function. At the minimum, this function must satisfy the values at both the beginning and end of the designated time interval, a requirement easily met by a rectilinear line.

However, to ensure a smoother trajectory that the drone can follow with minimal error, additional conditions must be addressed. These conditions encompass specifying the velocity and acceleration at the initial and final time instants. This necessitates the use of third and fifth-order polynomials, respectively. A firth-order polynomial was chosen offering enhanced flexibility in shaping trajectories and ensuring smoother transitions between waypoints, critical for the drone's trajectory tracking.

{{< katex display >}}
x(t) = a_5x^5 + a_4x^4 + a_3x^3 + a_2x^2 + a_1x + a_0
{{< /katex >}}

Each trajectory segment for every reference (including three coordinates and yaw) is described by a fifth-order polynomial. To achieve zero velocities and accelerations at the initial and final time instants, denoted as {{< katex >}}t_i{{< /katex >}} and {{< katex >}}t_f{{< /katex >}}, the following equation can be utilized:

\dot{x}(t_i) = \dot{x}(t_f) = \ddot{x}(t_i) = \ddot{x}(t_f) = 0

For waypoints situated along a rectilinear section of the trajectory, constant velocities to enhance lap time were chosen. Consequently, in such cases, the fifth-order polynomial simplifies into a rectilinear line, offering a straightforward and efficient path between these waypoints.

{{< details "Reference tracking for x,y,z and yaw for the Linear model with noise - (click to expand)" close >}}
![2](https://live.staticflickr.com/65535/53354670987_8a70ccb2c9.jpg)
{{< /details >}}

{{< hint example >}}
For better understanding, the trajectories were exemplified. Beginning marked with green and end marked with a red.
{{< /hint  >}}

Simple Square Trajectory ✔️
![3](https://live.staticflickr.com/65535/53355995060_dfec406836.jpg)

Crazy 8 Trajectory ✔️
![4](https://live.staticflickr.com/65535/53355542416_eee47fcfc0.jpg)

Amazing Race Trajectory ✔️
![5](https://live.staticflickr.com/65535/53355542411_d414fc8b0d.jpg)


### 5.3. 3D Virtual World 

The incorporation of a 3D virtual world into Simulink facilitated real-time visualization of the drone's movements. This block received position coordinates (x, y, z) along with pitch, roll, and yaw angles to visualize the drone's orientation. Additionally, to enhance the visual representation, wing rotation was considered, factoring in the voltage applied to each motor.

![6](https://live.staticflickr.com/65535/53355873959_5b8dddefa1_z.jpg)


{{< hint note >}}
**Virtual world representation*: Drone depicted at the origin, denoting its initial position. Notably, in this representation, the drone aligns with the x-axis forward, the y-axis to the right, and the z-axis downward, providing a clear orientation within the 3D virtual environment.
{{< /hint >}}

In the integrated virtual environment, a grid composed of squares, each measuring 1 meter per side, aids in visualizing the drone's movement in spatial terms. Moreover, adjustments were made to scale the drone's size, enhancing its representation to reflect a more realistic proportion within the virtual world.


## 6 LQR Results

During this phase, two potential outcomes were explored for each race trajectory, employing different dynamic models:

- Linear Model
- Nonlinear Model

To ensure the realism and stability of the drone's trajectories, certain restrictions were imposed on the LQR controller. These constraints aimed to create a more authentic representation of the drone's behaviour and enhance stability. The constraints employed were as follows:

- Actuation Values: Kept at or below 11.1 volts.
- Positional Errors: Restricted to remain consistently below 1 meter, while yaw error was constrained below 0.3 radians.
- Roll and Pitch Angles: Never allowed to exceed 0.8 radians (approximately 45 degrees) at any point throughout any trajectory.

These constraints were integral not only to uphold the integrity of the project but also to simulate the behaviour of a real drone more accurately. However, the primary objective and overarching goal of this project remained to **achieve the shortest possible time for each trajectory**.

### 6.1. Simple Square

In pursuit of achieving the lowest possible time for the simple square trajectory, the linear model was optimized to complete the path within 13 seconds, while the nonlinear model achieved a time of 12 seconds. It was aimed for 12 seconds as even a mere second holds substantial significance in a timed race scenario.

#### 6.1.1. Linear Model

In the linear model, the reference tracking displays high precision overall. However, when closely examined, minor oscillations become apparent in the tracking due to noise interference. This noise burdens the actuation, causing sporadic increases and creating an appearance of static. Nonetheless, our LQR and Kalman filter implementations effectively manage this, with the actuation peaking at around 10.1 Volts.

{{< details "Reference tracking for x,y,z and yaw for the linear model - (click to expand)" close >}}
![7](https://live.staticflickr.com/65535/53355873954_d023c7e995_c.jpg)
{{< /details >}}

The biggest error was obtained on the vertical movement (z axis) and it was of about 0.7 meters.

{{< details "Actuation (left) and errors (right) for the linear model - (click to expand)" close >}}
![8](https://live.staticflickr.com/65535/53355542376_b400732ed5_b.jpg)
{{< /details >}}

#### 6.1.2. Nonlinear Model

In the nonlinear model, significant discrepancies are evident in vertical position and yaw angle tracking. Although the tracking consistently returns to the reference, unexpected oscillations persist, leading to instability. Additionally, noise continues to impact actuation, peaking at around 10.4 Volts, despite eventual convergence to the reference trajectory.

{{< details "Reference tracking for x,y,z and yaw for the nonlinear model - (click to expand)" close >}}
![9](https://live.staticflickr.com/65535/53354670927_65a5f5e019_z.jpg)
{{< /details >}}

{{< details "Actuation (left) and errors (right) for the nonlinear model - (click to expand)" close >}}
![10](https://live.staticflickr.com/65535/53355765063_81c74da208_h.jpg)
{{< /details >}}

In comparison to the linear model, the errors in the nonlinear model appear more erratic but exhibit improved outcomes. Specifically, both lateral and vertical movements (y and z-axis) align more closely, reaching approximately 0.38 meters in matching discrepancies. The next table shows the constraint parameters values obtained for each model for the simple square.

|     LQR - Simple Square | Time (s) | Position Error (m) | Yaw Error (rad²) | Maximum Roll (rad) | Maximum Pitch (rad) |
|------------------------|-----------------------|---------------------|-------------------|--------------------|---------------------|
| Linear Model with Noise | 13                    | 0.1678              | 0.0008301         | 0.79               | 0.79                |
| Non-Linear Model with Noise | 12                 | 0.211               | 0.001614          | 0.69               | 0.76                |

The table highlights that with increased model complexity, errors and maximum values for roll and pitch also increase. However, the goal remained to push the LQR and Kalman filter to their limits without compromising the constraints set initially, aiming for the lowest time travel of 12 seconds. It's worth noting that achieving a shorter time travel implies higher velocities, leading to larger errors overall.

The following trajectory plots will showcase the reference, Linear Model, and Nonlinear Model for the Simple Square trajectory.

![11](https://live.staticflickr.com/65535/53355542341_361e37d8c5_c.jpg)

From previous observation, it's evident that the plots exhibit striking similarities, making it challenging to discern differences, ultimately indicating a successful tracking for the simple square trajectory.

### 6.2. Crazy 8

The Crazy 8 trajectory instructs the UAV to trace the shape of an "8," divided by the center, ascending along the z-axis in the first half and descending in the second half. 

#### 6.2.1. Linear Model

For the Linear model, a travel time of 17 seconds was assumed, yielding the following results.


{{< details "Reference tracking for x,y,z and yaw for the linear model - (click to expand)" close >}}
![12](https://live.staticflickr.com/65535/53354670892_321344cebd_z.jpg)
{{< /details >}}

{{< details "Actuation (left) and errors (right) for the linear model - (click to expand)" close >}}
![13](https://live.staticflickr.com/65535/53355542331_1c8e703797_b.jpg)
{{< /details >}}

Both figures illustrate the substantial influence of noise on actuation, resulting in pronounced instability. However, the Kalman Filter demonstrates remarkable effectiveness in managing this noise, as evidenced by only slight oscillations in pitch. While minor deviations from the reference trajectory might be observable upon close inspection, one is generally content with the results for the "Crazy 8" trajectory using the Linear model. The maximum errors for the linear model consistently hover around similar values, with the z-axis recording the highest error at approximately 0.3 meters.


#### 6.2.2. Nonlinear Model

Acknowledging that the race will employ the nonlinear model, extensive iterations were conducted to seek the shortest possible time for the trajectory. Ultimately, these efforts resulted in reducing the time travel by one second for the nonlinear model.

{{< details "Reference tracking for x,y,z and yaw for the nonlinear model - (click to expand)" close >}}
![14](https://live.staticflickr.com/65535/53355765013_2335df96e0.jpg)
{{< /details >}}

{{< details "Actuation (left) and errors (right) for the nonlinear model - (click to expand)" close >}}
![15](https://live.staticflickr.com/65535/53355765028_4345958912_h.jpg)
{{< /details >}}

Utilizing the Nonlinear model with noise, a 16-second trajectory duration was achieved. The drone showcased commendable reference tracking for longitudinal and lateral movements, though minor deviations were observed in vertical movement and yaw angle tracking.

Actuation spiked to the maximum constraint of 11 Volts due to noise-induced stress, enabling higher drone velocity for reduced travel time. However, this led to slightly less stable reference tracking, particularly in increased yaw angle oscillations. The roll and pitch exhibited differing behaviours compared to the linear model to accommodate nonlinear aspects.

Vertical movement displayed the highest discrepancy, reaching a maximum error of 0.5 meters.

The subsequent table outlines time values and constraints for each model during the Crazy 8 trajectory.

|   LQR - Crazy 8   | Time (s) | Position Error (m) | Yaw Error (rad²) | Maximum Roll (rad) | Maximum Pitch (rad) |
|-------------------|----------|---------------------|--------------------|---------------------|---------------------|
| Linear Model      | 17       | 0.2833              | 0.006547           | 0.7                 | 0.46                |
| Non-Linear Model  | 16       | 0.3241              | 0.00794            | 0.69                | 0.56                |

{{< hint note >}}
One might question why the Nonlinear model achieved a lower time value, but examining the errors and actuation provides insight into this. In the LQR controller, aiming for better trajectory times results in increased errors as the drone needs to maneuver faster, leading to sharper turns, and consequently, higher pitch and roll values.
{{< /hint >}}

{{< details "Crazy 8: linear trajectory (left) and nonlinear (right) - (click to expand)" close >}}
![16](https://live.staticflickr.com/65535/53355764983_221381b689_c.jpg)
{{< /details >}}


### 6.3. Amazing Race

The "Amazing Race" trajectory is designed for maximum speed through waypoints, mirroring real drone movements. Despite my efforts to optimize time while adhering to constraints, even reducing the time by one second risks violating one or all constraints. Testing revealed a minimum travel time of 60 seconds for both models. Following the Amazing Race section, a table will detail the constraint parameter values observed during this trajectory.

#### 6.3.1. Linear Model

{{< details "Reference tracking for x,y,z and yaw for the linear model - (click to expand)" close >}}
![17](https://live.staticflickr.com/65535/53355873844_8fa86a8fa1_z.jpg)
{{< /details >}}

{{< details "Actuation (left) and errors (right) for the linear model - (click to expand)" close >}}
![18](https://live.staticflickr.com/65535/53355764958_234ee1ff65_c.jpg)
{{< /details >}}


Once more, the LQR demonstrates precise tracking along the Amazing Race trajectory. However, spikes persist in the actuation, induced by the LQR's continuous adjustments to counter noise-induced changes. Despite these spikes, none of the constraints are breached, with the maximum actuation spike recorded at approximately 10 Volts. The complexity of the task led to more erratic error behavior, although overall results remained satisfactory, with the maximum error reaching around 0.4 rad² for the yaw angle.


##### 6.3.2. Nonlinear Model

{{< details "Reference tracking for x,y,z and yaw for the nonlinear model - (click to expand)" close >}}
![19](https://live.staticflickr.com/65535/53355994920_3cb9865d0f_z.jpg)
{{< /details >}}

{{< details "Actuation (left) and errors (right) for the nonlinear model - (click to expand)" close >}}
![20](https://live.staticflickr.com/65535/53355764943_80af387537_h.jpg)
{{< /details >}}

In the Nonlinear model, deviations start surfacing during reference tracking, causing occasional slight drifts from the reference, although the drone ultimately reconverges to it. Despite this, the overall tracking throughout the trajectory remains relatively accurate. However, mitigating these deviations would entail increasing the drone's travel time, yet in a race, every second counts. Additionally, the maximum values for roll and pitch noticeably escalate. A reduction of just one second along the path could surpass the 0.8 rad (45º) constraint, leading to the drone's descent—something bound to avoid.

Regarding actuation, the expected pattern persists, with a spike reaching around 10 Volts. The errors between the Linear and Nonlinear models showcase similar trends, with lateral and vertical movements being the primary offenders, reaching a maximum error of approximately 0.4 meters.

The subsequent table presents the constraint parameter values for each model studied during the Amazing Race trajectory.

| LQR - Amazing Race| Time (s) | Position Error (m) | Yaw Error (rad²) | Maximum Roll (rad) | Maximum Pitch (rad) |
|-------------------|----------|---------------------|------------------|---------------------|---------------------|
| Linear Model      | 60       | 0.1509              | 0.005645         | 0.48                | 0.7                 |
| Non-Linear Model  | 60       | 0.1613              | 0.005131         | 0.74                | 0.67                |

{{< details "Amazing Race: linear trajectory (left) and nonlinear (right) - (click to expand)" close >}}
![21](https://live.staticflickr.com/65535/53354670757_5440ff4e5d_c.jpg)
{{< /details >}}

{{< hint note >}}
As depicted, the plots exhibit a similar overall shape but manifest differences throughout the trajectory. Notably, in the second-to-last curve, the reference demands a sharp turn from the drone. However, the designed controller struggles to match this curve precisely, causing the drone to execute a wider turn instead. Despite this, our thorough waypoint checks along the path lead us to consider this Amazing Race with a 60-second travel time a resounding success!
{{< /hint >}}

## 7 MPC Results

### 7.1. Simple Square

#### 7.1.1. Linear Model


In selecting a 25-second duration for the Simple Square trajectory, maintaining this duration was crucial to minimize errors and uphold tracking accuracy. The Linear model showcased commendable reference tracking, as illustrated. The image indicates precise reference tracking by the drone, while also adhering to the constraints for pitch and roll.


{{< details "Reference tracking for x,y,z and yaw for the linear model - (click to expand)" close >}}
![22](https://live.staticflickr.com/65535/53355542231_0995dde3d6_z.jpg)
{{< /details >}}

From the actuation plot, it is observed the admissible limits and the maximum position error is near 0.6 meters.

{{< details "Actuation (left) and errors (right) for the linear model - (click to expand)" close >}}
![23](https://live.staticflickr.com/65535/53354670762_f0a38a7397_h.jpg)
{{< /details >}}

{{< details "3D Trajectory for the linear model - (click to expand)" close >}}
![24](https://live.staticflickr.com/65535/53355994855_ea81fea746_c.jpg)
{{< /details >}}

#### 7.1.2. Nonlinear Model

The Nonlinear model exhibited reference tracking akin to the Linear model, maintaining accurate tracking and adhering to the constraints. However, there were slight variations observed in the pitch and roll, displaying slightly more variability compared to the Linear model.

{{< details "Reference tracking for x,y,z and yaw for the nonlinear model - (click to expand)" close >}}
![25](https://live.staticflickr.com/65535/53355994875_7e049c49c3_c.jpg)
{{< /details >}}

From the actuation plot, it is observed the admissible limits and the maximum position error almost the same as in the linear model near 0.6 meters, however in the nonlinear model it can be seen a local maximus that wasn't not detected in the linear model.

{{< details "Actuation (left) and errors (right) for the nonlinear model - (click to expand)" close >}}
![26](https://live.staticflickr.com/65535/53355542181_895e382c00_h.jpg)
{{< /details >}}

{{< details "3D Trajectory for the nonlinear model - (click to expand)" close >}}
![27](https://live.staticflickr.com/65535/53355542166_2844b98a84_c.jpg)
{{< /details >}}

|  MPC - Simple Square| Time (s) | Position Error (m) | Yaw Error (rad^2) |
|-------------------|----------|---------------------|-------------------|
| Linear Model      | 25       | 0.1682              | 0.0002414         |
| Non-Linear Model  | 25       | 0.1908              | 0.0002915         |


### 7.2. Crazy 8

#### 7.2.1. Linear Model

A duration of 35 seconds was selected for the Crazy 8 trajectory utilizing MPC. Once more, commendable reference tracking was observed, and the constraints for the angles remained effective. Additionally, there were no motor saturations, with the maximum actuation reaching approximately 8.2 Volts. The maximum error recorded was 0.5 meters.

{{< details "Reference tracking for x,y,z and yaw for the linear model - (click to expand)" close >}}
![28](https://live.staticflickr.com/65535/53354670722_a77a034178_c.jpg)
{{< /details >}}

From the actuation plot, it is observed the admissible limits and the maximum position error is near 0.6 meters.

{{< details "Actuation (left) and errors (right) for the linear model - (click to expand)" close >}}
![29](https://live.staticflickr.com/65535/53354670727_a4ecbedfb0_h.jpg)
{{< /details >}}

{{< details "3D Trajectory for the linear model - (click to expand)" close >}}
![30](https://live.staticflickr.com/65535/53355542161_566408be60_z.jpg)
{{< /details >}}

#### 7.2.2. Nonlinear Model

The Nonlinear model similarly yielded positive outcomes. Reference tracking remained accurate, maintaining constraints within the angles. Additionally, there were no instances of motor saturation, with the maximum actuation reaching around 8.4 Volts. The maximum error observed was 0.6 meters. 


{{< details "Reference tracking for x,y,z and yaw for the nonlinear model - (click to expand)" close >}}
![31](https://live.staticflickr.com/65535/53355873704_6b5f56006b_b.jpg)
{{< /details >}}

{{< details "Actuation (left) and errors (right) for the nonlinear model - (click to expand)" close >}}
![32](https://live.staticflickr.com/65535/53355994820_a232088b14_h.jpg)
{{< /details >}}

{{< details "3D Trajectory for the nonlinear model - (click to expand)" close >}}
![33](https://live.staticflickr.com/65535/53355542101_43c019bb4b_c.jpg)
{{< /details >}}

|  MPC - Crazy 8    | Time (s) | Position Error (m) | Yaw Error (rad^2) |
|-------------------|----------|---------------------|-------------------|
| Linear Model      | 35       | 0.2485              | 0.001326          |
| Non-Linear Model  | 35       | 0.2866              | 0.001438          |


### 7.3. Amazing Race

#### 7.3.1. Linear Model

In the case of the largest track, a lap time of 90 seconds was opted for using MPC. While reducing the time was possible, it would inevitably lead to errors surpassing acceptable values. Throughout this duration, reference tracking remained commendable, with a maximum error of 0.6 meters. No motor saturation was observed. Figures 34 and 35 illustrate the tracking, actuation, errors, and trajectory. Additionally, Figure 36 displays the 3D trajectory of the linear model.

{{< details "Reference tracking for x,y,z and yaw for the linear model - (click to expand)" close >}}
![34](https://live.staticflickr.com/65535/53355542111_6893298d86_c.jpg)
{{< /details >}}

{{< details "Actuation (left) and errors (right) for the linear model - (click to expand)" close >}}
![35](https://live.staticflickr.com/65535/53355764758_cc16453653_h.jpg)
{{< /details >}}

{{< details "3D Trajectory for the linear model - (click to expand)" close >}}
![36](https://live.staticflickr.com/65535/53355764738_7dba3cfa2e_z.jpg)
{{< /details >}}

#### 7.3.2. Nonlinear Model

The outcomes for the Nonlinear model remain positive. Reference tracking sustained its accuracy, adhering to constraints within the angles. Additionally, no motor saturation was noted, with the maximum actuation reaching approximately 8.5 Volts. The maximum error recorded was 0.8 meters.

{{< details "Reference tracking for x,y,z and yaw for the nonlinear model - (click to expand)" close >}}
![37](https://live.staticflickr.com/65535/53354670662_7c53c1574b_c.jpg)
{{< /details >}}

{{< details "Actuation (left) and errors (right) for the nonlinear model - (click to expand)" close >}}
![38](https://live.staticflickr.com/65535/53355994765_285c1bc027_b.jpg)
{{< /details >}}

{{< details "3D Trajectory for the nonlinear model - (click to expand)" close >}}
![39](https://live.staticflickr.com/65535/53355764733_621636e616.jpg)
{{< /details >}}

| MPC - Amazing Race | Time (s) | Position Error (m) | Yaw Error (rad^2) |
|-------------------|----------|---------------------|-------------------|
| Linear Model      | 90       | 0.2182              | 0.003338          |
| Non-Linear Model  | 90       | 0.252               | 0.003468          |


## 8 Amazing Race

In this phase, the goal was exploring drone modeling, controller design, and trajectory racing. The initial phase focused on acquiring the drone's complete and linearized models under hovering conditions, with varied success in initial controller attempts.

In the subsequent phase, emphasis shifted towards controller design using pole placement and LQR methodologies, alongside studying sensor noise effects and employing the Kalman filter. Results improved notably, especially with the Kalman filter's advantage in simulating measurement noise. A preliminary attempt at navigating a more complex trajectory also yielded promising outcomes.

The third phase introduced a more realistic scenario, implementing controllers and observers in discrete time. 

![00](https://live.staticflickr.com/65535/53356198050_d00791b401_c.jpg)

Tests mirrored real-world scenarios, with actual trajectories used for simulations and a competitive aspect to achieve optimal results based on accumulated knowledge.

![2200](https://live.staticflickr.com/65535/53354870627_0d88b0246e_c.jpg)

Controllers based on optimization methods (LQR and MPC) were prioritized, while the pole placement method and Luenberger estimator were discarded due to their suboptimal performance, especially in the presence of noise.

![1221](https://live.staticflickr.com/65535/53356074154_d49b07a58e_c.jpg)

![21301](https://live.staticflickr.com/65535/53354870622_fa356dfce1_c.jpg)

![21312](https://live.staticflickr.com/65535/53356074149_4948cf477d_c.jpg)

Trajectories emerged as critical design parameters, impacting performance significantly. Results showed a consistent trade-off: achieving faster travel times increased velocities, leading to larger errors — a finding consistent across both controllers. Broadly, the LQR consistently delivered faster times across all trajectories. Conversely, the MPC facilitated a more stable drone behaviour by incorporating constraints on roll and pitch values. Detailed results are outlined in the table below.


![21ss](https://live.staticflickr.com/65535/53356198045_4db1cceaf9_c.jpg)

![1240ss](https://live.staticflickr.com/65535/53354870612_b95ccfae7d_c.jpg)


| Nonlinear | LQR Position Error (m) | LQR Yaw Error (rad^2) | LQR Time (s) | MPC Position Error (m) | MPC Yaw Error (rad^2) | MPC Time (s) |
|----------------------|------------------------|-----------------------|--------------|------------------------|-----------------------|--------------|
| Simple Square        | 0.2110                 | 0.001614              | 12           | 0.1908                 | 0.0002915             | 25           |
| Crazy 8              | 0.3241                 | 0.00794               | 16           | 0.2866                 | 0.001438              | 35           |
| Amazing Race         | 0.1613                 | 0.005131              | 60           | 0.2520                 | 0.003468              | 90           |


Using the [MATLAB graphical interface for MPC design](https://www.mathworks.com/products/model-predictive-control.html) led to a potential loss of sensitivity when fine-tuning it. Consequently, the MPC might hold untapped potential not fully explored in this project, presenting an area for further improvement and exploration.

{{< hint tip >}}
[Advance to UAV Computer Vision Here ✌️](https://ricardochin.com/docs/code/uav/computer-vision/){{< /hint >}}
