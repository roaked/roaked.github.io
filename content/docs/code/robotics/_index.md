---
title: "Robotics: Kin, Dynamics & Control"
weight: 7
---

# KUKA: From Kinematics to Motion Planning

[To add report when I have time :)](https://github.com/roaked/robotics-kinematics-dynamics-and-control)

![soakd](https://blogs.mathworks.com/images/seth/2009Q2/Robot.gif)

{{< hint important >}}

- I would at some point like to implement a safe reinforcement learning whilst heading for optimistic exploration within a large-scale manufacturing setting instead of defining a specific trajectory! 

I think it would turn out a very informative and interesting project!
{{< /hint >}}


## 1. Abstract

This research centers on the kinematic analysis of the KUKA KR6 R700 robot, which is installed on a linear KL100 axis. The KUKA KR6 R700 is an industrial robot designed for tasks such as tool and fixture handling, as well as processing or transferring components or products.

The initial phase of studying the robot involves analyzing and constructing its kinematic model based on the Denavit-Hartenberg convention, with a focus on selecting the link reference frames. 

Presented and validated through various Simulink models, it is intended to ease the extraction of the robot's direct kinematics, inverse kinematics, geometric Jacobian, and Closed Loop Inverse Kinematics (CLIK).

## 2. Kinematics

The robot consists on the presence of 6 revolute joints. Additionally, there is a single prismatic joint that facilitates horizontal movement of the robot. Consequently, the overall configuration of the robot comprises a total of 7 links.

![1](https://live.staticflickr.com/65535/53469950810_c67521134d.jpg)

![2](https://live.staticflickr.com/65535/53469851949_b3914f719c.jpg)

Utilizing the convention of [Denavit-Hartenberg (DH)](https://en.wikipedia.org/wiki/Denavit%E2%80%93Hartenberg_parameters), reference frames for each link can be drawn.

![3](https://live.staticflickr.com/65535/53469851939_221cf7f814.jpg)

The dimensions `a2`, `a3`, `a4`, `d5` and `d7` are known. Hence, the table of DH can be built.

| Link | {{<katex>}}d_i{{</katex>}} [\m] | {{<katex>}}\vartheta_i{{</katex>}} [\rad] | {{<katex>}}a_i{{</katex>}} [\m] | {{<katex>}}\alpha_i{{</katex>}} [\rad] | offset [\rad] |
|------|-------------|-----------------------|------------|---------------------|------------------------|
| 1    | {{<katex>}}d_1{{</katex>}}      | 0                     | 0          | {{<katex>}}-\frac{\pi}{2}{{</katex>}}   | 0                      |
| 2    | 0           | {{<katex>}}\vartheta_2{{</katex>}}        | 0.025      | {{<katex>}}\frac{\pi}{2}{{</katex>}}   | 0                      |
| 3    | 0           | {{<katex>}}\vartheta_3{{</katex>}}        | 0.315      | 0                   | 0                      |
| 4    | 0           | {{<katex>}}\vartheta_4{{</katex>}}        | 0.035      | {{<katex>}}\frac{\pi}{2}{{</katex>}}   | 0                      |
| 5    | -0.365      | {{<katex>}}\vartheta_5{{</katex>}}        | 0          | {{<katex>}}-\frac{\pi}{2}{{</katex>}}   | 0                      |
| 6    | 0           | {{<katex>}}\vartheta_6{{</katex>}}        | 0          | {{<katex>}}\frac{\pi}{2}{{</katex>}}    | 0                      |
| 7    | -0.080      | {{<katex>}}\vartheta_7{{</katex>}}        | 0          | {{<katex>}}\pi{{</katex>}}              | 0                      |



For this robot, the vector of joint variables `q` can be expressed as:

{{< katex display >}}
q = [d1 \hspace{.2cm} \vartheta_2 \hspace{.2cm} \vartheta_3 \hspace{.2cm} \vartheta_4 \hspace{.2cm} \vartheta_5 \hspace{.2cm} \vartheta_6 \hspace{.2cm} \vartheta_7 ]^T
{{< /katex >}}

## 3. Direct Kinematics

Through the direct kinematics analysis, one can compute the position and orientation of the end effector by considering the values of the joint variables within the vector `q`. 

{{< katex display >}}
A_{i}^{i-1} = \begin{bmatrix}
    R_{i}^{i-1} & r_{i-1, i}^{i-1} \\
    0^T & 1
\end{bmatrix}
{{< /katex >}}

`R` being the rotation matrix from link `i-1` to `i` and `r` the position vector from link `i-1` to `i`. Generalizing for a joint variable {{< katex >}}\vartheta_i{{< /katex >}}:

{{< katex display >}}
A_{i-1}^{i}(\theta_i) = \begin{bmatrix}
    c_i & -s_i c_i & s_i c_i & a_i c_i \\
    s_i & c_i c_i & -c_i s_i & a_i s_i \\
    0 & s_i & c_i & d_i \\
    0 & 0 & 0 & 1
\end{bmatrix}
{{< /katex >}}

The transformation matrix for 7 links:


{{< katex display >}}
T_7^0 = A_1^0 A_2^1 A_3^2 A_4^3 A_4^5 A_6^5 A_7^6
{{< /katex >}}


In MATLAB, the calculations described are executed using the functions [DKin.m](https://github.com/roaked/robotics-kinematics-dynamics-and-control/blob/main/DKin.m) and [DHTransf.m](https://github.com/roaked/robotics-kinematics-dynamics-and-control/blob/main/DHTransf.m). These functions were initially provided as a toolbox, and [DKin.m](https://github.com/roaked/robotics-kinematics-dynamics-and-control/blob/main/DKin.m) was modified to include the computation of the Jacobian matrix for future use. A MATLAB Function Block was then crafted from these functions, integrated into the Simulink model. This block takes the `q` vector as inputs and outputs the rotation matrix, position vector, and Jacobian matrix.

Another essential function, [vrrobot.m](https://github.com/roaked/robotics-kinematics-dynamics-and-control/blob/main/vrrobot.m), is employed to create a virtual representation of the robot within a simulated environment. By adding meshes to the links, the robot gains a tangible form beyond just reference frames.

The resulting Simulink model incorporates two input options for the `q` vector: sliders or trajectory. The `q` vector is fed into both the direct kinematics block and the animation block (VR sink), providing a comprehensive visualization of the robot's movements.


![4](https://live.staticflickr.com/65535/53469950785_90a2919709_c.jpg)


### 3.1. Validation

#### 3.1.1. Pose One

The initial validation involves inputting a set of known coordinates, denoted as `q`, and verifying if the resulting rotation matrix and position vector of the end effector align with the anticipated outcomes. During this validation process, certain adjustments to the offsets were necessary. Specifically, a -90º offset was applied to joint `q4`, and a +180º offset was applied to joint `q7`, with these values determined through the validation process. To define the `q` vector, sliders were employed.

A significant consideration is that the base reference frame used for validation differs from the zeroth frame defined in the kinematic model (frame 0). This distinction is illustrated when observing the robot in the virtual world. To enhance representation, the x-axis is depicted in red, the y-axis in green, and the z-axis in blue. Additionally, a distance d1 was set to separate the robot from frame 0, ensuring accurate validation.

![5](https://live.staticflickr.com/65535/53469533961_844a2cbdc2_z.jpg)

Thus, a transformation from the base reference frame to frame 0 becomes necessary, encompassing both rotation and translation. The rotational aspect is characterized by:


{{< katex display >}}
R_{0}^b = \begin{bmatrix}
    0 & 0 & -1 \\
    1 & 0 & 0 \\
    0 & -1 & 0
\end{bmatrix}
{{< /katex >}}

Therefore the rotation matrix from base to end effector is defined as:

{{< katex display >}}
R_7^b = R_0^b R_7^0
{{< /katex >}}

Simultaneously, the translation using the position vector:

{{< katex display >}}
r_{b,0}^b = [0 \hspace{.2cm} 0 \hscape{.2cm} 0.756 ]^T
{{< /katex >}}

The position vector from base to end effector is defined as:

{{< katex display >}}
r_{b,7}^b = r_{b,0}^b + r_{0,7}^b = r_{b,0}^b + R_{0}^b \hscape{.1cm} r_{0,7}^0
{{< /katex >}}

Analytical results are:

{{< katex display >}}
R_{7}^b = \begin{bmatrix}
    0 & -1 & 0 \\
    0 & 0 & 1 \\
    -1 & 0 & 0
\end{bmatrix}
{{< /katex >}}

{{< katex display >}}
r_{b,7}^b = [0 \hscape{.2cm} 0.470 \hscape{.2cm} 1.106]^T [\m] 
{{< /katex >}}

Simulink results given by:

![6](https://live.staticflickr.com/65535/53469851914_780545e45b_z.jpg)

![7](https://live.staticflickr.com/65535/53468628947_f0e300b867_c.jpg)


#### 3.1.2. Pose Two

Analytical results are:

{{< katex display >}}
R_{7}^b = \begin{bmatrix}
    0.249 & -0.457 & 0.854 \\
    -0.957 & -0.251 & 0.145 \\
    0.148 & -0.853 & -0.500
\end{bmatrix}
{{< /katex >}}

{{< katex display >}}
r_{b,7}^b = [0.20163 \hscape{.2cm} 0.44482 \hscape{.2cm}  0.97382]^T [\m] 
{{< /katex >}}

Simulink results given by:

![8](https://live.staticflickr.com/65535/53469533951_c423d1b57f_z.jpg)

![9](https://live.staticflickr.com/65535/53469533956_5b76d48ea0_c.jpg)


### 3.2. Joint Trajectories

To conduct further testing of the direct kinematics, a series of joint trajectories was incorporated into the Simulink model, selectable by manipulating a manual switch. Initially, a set of ramps was chosen for the inputs, with the exception of joint `q1`, where a sine input was employed. This decision was made to avoid impractical robot movement away from the base, given the nature of a ramp input. Each ramp trajectory was individually examined to ensure that the robot's motion aligned with the anticipated behaviour.

For instance, a specific scenario involved fixing all joints except for `q2`, which theoretically should result in the robot spinning around itself. To validate this trajectory, an XY plot was generated from the base reference frame to visually confirm the expected movement.

![10](https://live.staticflickr.com/65535/53469851894_5d06d7bd7d_b.jpg)


Another test involved applying sinusoidal inputs to all joints simultaneously. This particular test proved valuable as it enabled the movement of all joints concurrently without the risk of robot links intersecting or colliding with each other, a scenario not feasible in a physical robot. 

![11](https://live.staticflickr.com/65535/53469533946_d84d6bd8d9.jpg)

## 4. Inverse Kinematics

Inverse kinematics involves determining the values of the joint variables `q`, given the position and orientation of the end effector. It's important to note that solving such problems may yield multiple solutions.

In the case of the KR6, the end effector's position and orientation are described by 6 parameters: three displacements (`x`, `y`, `z`) and three Euler angles ({{< katex >}}\phi{{</ katex >}}, {{< katex >}}\theta{{</ katex >}}, {{< katex >}}\psi{{</ katex >}}). However, the robot has 7 links, indicating it possesses 7 degrees of freedom. This redundancy implies that, to solve the inverse kinematics problem, one of the joint variables must be specified initially. In this scenario, `q1`, corresponding to the prismatic joint, was chosen to be fixed, leaving 6 degrees of freedom to be determined.

To address this, a kinematic decoupling approach was adopted. This involves separating the arm and wrist, solving the arm joints first and then addressing the wrist joints. This strategy simplifies the inverse kinematics problem and facilitates a systematic solution to determine the joint variables.

![12](https://live.staticflickr.com/65535/53469672968_55e4fe8b0c_c.jpg)


The wrist's position serves as a reference point for calculating the initial four parameters of the robot. Beginning with the end effector's position, the wrist's position can be computed.

{{< katex display >}}
p_w = p_{07} - d_7 \times z_7^0 - [\begin{bmatrix} 0 \\ 0 \\ q_1 \end{bmatrix}]
{{< /katex >}}

It's crucial to note that all subsequent calculations utilize reference frame 0', which corresponds to reference frame 0 translated with `q1`. Additionally, in these equations, `d5` and `d7` are assumed to be positive values. Furthermore, the equations provided are applicable for a shoulder-up configuration.

![13](https://live.staticflickr.com/65535/53468628917_1d8779ccb6_z.jpg)

![14](https://live.staticflickr.com/65535/53469851864_32e835bddb_z.jpg)

After extensive trigonometric algebra, {{< \katex >}}\alpha{{< /katex >}} and {{< \katex >}}\beta{{< /katex >}} are computed:

{{< katex display >}}
\alpha = atan2 (- p_{wy}, \sqrt{p_{wx}^2 + p_{wz}^2} - a_2)
{{< /katex >}}

{{< katex display >}}
\beta = atan2 (a_w \sqrt{1-c_w^2}, a_3 + a_w c_w)
{{< /katex >}}

{{< hint important >}}
After computing `q1`, `q2`, `q3` and `q4` only the wrist parameters are left to find. Hence, using rotation matrix 4 -> 7, it is possible to compute `q5`, `q6` and `q7`.
{{< /hint >}}

![15](https://live.staticflickr.com/65535/53469851869_a78896bf27_c.jpg)

The system offers two input options for `pd` and `Rd`:

- Option 1: Users can specify the end effector position and orientation using parameters `x`, `y`, `z` (combined to form `pd`) and the Euler angles {{< katex>}}\phi{{</ katex>}}, {{< katex>}}\theta{{</ katex>}}, {{< katex >}}\psi{{</ katex>}}. The rotation matrix `Rd` is derived from these Euler angles.

- Option 2: Users can input joint variables `q`, which are then processed and transformed into `pd` and `Rd` through direct kinematics. This option is particularly useful for validation purposes.

To select either Option 1 or Option 2, two manual switches are provided. Both switches should be consistently either up (Option 1) or down (Option 2). Once the option is chosen, `pd` and `Rd` are transformed into `p07` and `R07`.

The inverse kinematics block accepts four inputs: `p07`, `R07`, `q1`, and a vector defining the robot's configuration. The configuration vector should consist of integers 0 or 1, representing:

1. The elbow's orientation (1 for up, 0 for down).
2. The wrist's s6 value (1 for positive, 0 for negative).
3. The robot's orientation (1 for facing forwards, 0 for facing backward).
4. An additional number to address out-of-boundary positions when `q1` is fixed. Setting this value to 1 allows `q1` to take the inputted value, while 0 aligns `q1` with `xb` from the end effector position relative to the base reference frame — a "free range" configuration for `q1`.

The output of the inverse kinematics block is the joints vector `q`, which feeds into the animation block (VR sink) and a direct kinematics block that outputs the obtained `R` and `p`. `q` is also displayed in a block labeled "vector `q`". For validation purposes, a comparison is made between `pd` and `Rd` (desired) and `p` and `R` (obtained).

A switch at the bottom-left corner can be activated to halt the simulation when the robot encounters a singularity.

### 4.1. Validation

To add later...

## 5. Geometric Jacobian


### 5.1. Postion Validation

### 5.2. Orientation Validation

### 5.3. Kinematic Singularities

## 6. Closed Loop Inverse Kinematics (CLIK)

### 6.1. Validation

## 7. Link Properties

To incorporate the dynamics of the robot, it's essential to determine approximate values for the mechanical properties of the links. Solidworks, with its available CAD models, was employed for this purpose. Assigning a material in Solidworks is a crucial step, and since the CAD models represented solid bodies instead of hollow structures, it was necessary to adjust the density.

Given that the robot's manual specified a weight of 50 kilograms for the KR6 robot without the carriage and KL beam, a uniform density of 1680 kg/m³ was applied in Solidworks. This density adjustment resulted in the KR6 parts weighing approximately 50.4 kilograms, providing a satisfactory approximation. It's noteworthy that link 1 comprises two parts: the base of KR6 and a carriage supporting KR6. In the manual, the carriage weight is not included in the KR6's weight specification.

| Link | Mass [\kg] | x | y | z | Ixx | Iyy | Izz | Ixy | Ixz | Iyz |
|------|-----------|---|---|---|-----|-----|-----|-----|-----|-----|
| 1    | 54.153    | 0.0004   | -0.0005  | 0.4416  | 1.2211  | 1.3092  | 1.0273  | -0.0033  | -0.1784  | 0.0012   |
| 2    | 10.526    | -0.0170  | 0.0674   | 0.0020  | 0.1277  | 0.0943  | 0.0840  | 0.0089  | 0       | 0.0009   |
| 3    | 12.299    | -0.1840  | -0.0053  | -0.0058 | 0.0639  | 0.2515  | 0.2255  | 0.0015  | 0.0028  | 0        |
| 4    | 4.810     | -0.0246  | 0        | -0.0161 | 0.0164  | 0.0186  | 0.0136  | 0       | 0.0023  | 0        |
| 5    | 4.582     | 0.0001   | -0.1341  | 0.0036  | 0.0287  | 0.0096  | 0.0259  | 0       | 0       | -0.0003  |
| 6    | 0.747     | 0        | 0.0019   | -0.0168 | 0.0010  | 0.0009  | 0.0005  | 0       | 0       | 0        |
| 7    | 0.023     | 0        | 0        | -0.0075 | 3E-6    | 3E-6    | 5E-6    | 0       | 0       | 0        |


The inclusion of motors within the robot's links was modeled by representing them as cylinders with a diameter of 1 cm and a height of 10 cm, constructed from steel. The resulting inertia of the motors around their z-axis (Im) was determined to be 0.00021 kg m². Additionally, gear reduction ratios (`kr`) were set at a value of 100. 

## 8. Newton-Euler Formulation

The Newton-Euler formulation relies on the equilibrium of forces acting on the manipulator's links. This formulation enables a recursive solution, divided into two sets of recursion:

- 1. **Forward Recursion**: This phase propagates and computes link velocities and accelerations, commencing from link 1 and concluding at the end-effector.

- 2. **Backward Recursion**: In this phase, forces for prismatic joints and moments for revolute joints are propagated and calculated. The recursion begins at the end-effector and concludes at link 1.


{{< hint tip >}}
A key advantage of the Newton-Euler formulation lies in its simplicity of implementation, facilitating an efficient computation of the dynamics solution.
{{< /hint >}}

The underlying concept of the Newton-Euler formulation is as follows: the forward recursion helps calculate velocities and accelerations, enabling the determination of end-effector forces and moments. Subsequently, by proceeding backward, each force and moment for the link preceding the end-effector is computed. This approach allows for the determination of all forces and moments acting on the links. With this information, the behaviour of torque joints for any trajectory can be calculated. Utilizing the torque joints, and assuming no external forces at the end-effector, the joint space dynamic model can be established.

{{< katex display >}}
B(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = \tau
{{< /katex >}}

In order to calculate the torques, the manipulator equation can be decomposed into three main components: the mass matrix `B`, the Coriolis/Centrifugal matrix `C` and a given gravity vector `g`. The term {{< katex >}} C(q, \dot{q})\dot{q} {{< /katex >}} can be expressed as a vector {{< katex >}} \phi (q, \dot{q}) {{< /katex >}}.

Implementing these calculations in real-time requires a dynamics block in Simulink. [A general Newton-Euler equation was developed in MATLAB](https://github.com/roaked/robotics-kinematics-dynamics-and-control/tree/main/DynamicsV1), capable of [solving the Newton-Euler formulation](https://github.com/roaked/robotics-kinematics-dynamics-and-control/blob/main/DynamicsV1/Newton_Euler.m) for any robot with both revolute and prismatic joints. This equation takes the Denavit-Hartenberg (DH) table, along with the necessary properties of links and motors, and outputs the torques.

Using the function, it is possible to obtain `B`, {{< katex >}}\phi{{< /katex >}}, and `g`. The gravity vector is the simplest to obtain: {{< katex >}}g = \tau (q, \dot{q}, q\ddot{q}) = \tau (q, 0, 0){{< /katex >}}. Imposing the joints velocities and accelerations to zero, `B` and `C` are erased from the equation.

To obtain the mass matrix, since it doesn’t depend on the velocity, {{< katex >}}\dot{q}{{< /katex >}} can be set to zero. The acceleration of gravity is also turned to zero, leaving the equation as {{< katex >}}B(q\dot{q})\ddot{q} = \tau {{< /katex >}}. Deriving the expressions by the vector {{< katex >}}\ddot{q}{{<  /katex >}}, `B` is finally obtained.

{{< katex >}}\phi = C(q, \dot{q})\dot{q}{{< /katex >}} is obtained by setting {{< katex >}}\ddot{q}{{< /katex >}} and the gravity acceleration to zero.

Regarding `B` and {{< katex >}}\phi{{< /katex >}}, another separation can be made. Both parameters can be divided by the contributions of the augmented links, {{< katex >}}B_l{{< /katex >}} and {{< katex >}}\phi_l{{< /katex >}}, and contributions of the motors, {{< katex >}}B_m{{< /katex >}} and {{< katex >}}\phi_m{{<  /katex >}}. To obtain the contributions of the links, the mechanical properties of the motors {{< katex >}}k_r{{< /katex >}} and {{< katex >}}I_m{{< /katex >}} can be set to zero, while to obtain the motors' contributions the masses of the links `m` and their inertia tensors `I` can be set to zero. This separation can be useful to visualize the influence of the motors on these parameters.

### 8.1. Validation


![50](https://live.staticflickr.com/65535/53468756072_f11d1ac5a6_b.jpg)

The dynamics block takes the position and velocity vectors, q and {{< katex >}}\dot{q}{{< /katex >}}, as inputs and produces `B` and {{< katex >}}\phi{{< /katex >}} (with separated terms for contributions from links and motors) along with `g` as outputs. Accelerations are computed and then integrated to derive velocities, which are subsequently integrated to obtain positions.

{{< katex display >}}
\ddot{q} = B^{-1}(\tau - \phi - g)
{{< /katex >}}

By setting the torques to zero, the robot undergoes motion solely due to the influence of gravity. The nature of this movement is contingent upon the initial conditions of the robot. For instance, with initial conditions {{< katex >}}q_0 = [ 0,0,0, −\frac{\pi}{2}, 0,0,0 ]{{< /katex >}}, the robot exhibits pendulum-like behavior, with the arm fully extended. Absent viscous forces, mechanical energy is conserved, leading to sustained oscillations. Alternatively, setting the initial conditions to {{< katex >}}q_0 = [ 0,\frac{\pi}{2},0,0 0,0,0 ]{{< /katex >}} results in the arm behaving akin to a double pendulum as subsequently shown.

![51](https://live.staticflickr.com/65535/53469800668_6e985fb8b5.jpg)


## 9. Decentralized PID Joint Controllers

The central concept behind the decentralized controller involves breaking down the manipulator into `n` independent systems, where `n` represents the number of joints. Each joint is then controlled independently. The proposed solution entails the creation of a closed-loop PID (Proportional-Integral-Derivative) controller for each joint. In this setup, the input is the desired joint value `qd` and the output is the `q` values, which the KR6 Robot uses for computation. This approach relies on the equation:

{{< katex display >}}
\tau = -K_d \dot{q} + K_p(q - q_d) - K_i \int (q - q_d) \, dt
{{< /katex >}}

The integral action in the PID controller serves to eliminate the persistent error induced by the simulated gravity. Consequently, instead of using the integral gain, replace it by directly incorporating the manipulator's gravity compensation outputs for each joint. This adjustment helps counteract the influence of simulated gravity and contributes to improved control performance.

{{< katex display >}}
\tau = -K_d \dot{q} + K_p(q - q_d) + g(q)
{{< /katex >}}

Opting for a PD (Proportional-Derivative) controller without an integral gain is beneficial as it avoids introducing a pole at the origin, which could otherwise slow down the system response. The chosen approach involves creating a PD controller and incorporating gravity compensation. In this setup, the dynamics manipulator receives an initial condition and produces the joint velocities {{< katex >}} `\dot{q}` {{< /katex >}}, joint values `q` and gravity compensation. These outputs feed into the controller, which computes a new torque {{< katex >}} `\tau` {{< /katex >}}. This torque is then fed back into the dynamics manipulator, resulting in updated joint values `q` that are subsequently utilized in the actual robot. This iterative process helps refine the control of the system.

![52](https://live.staticflickr.com/65535/53468756057_84d50fd68c_z.jpg)

### 9.1. Stifness and Damping Gains

To determine the values for the Stiffness `Kp` and Damping `Kd` gains:

{{< katex display >}}
B(q)\ddot{q} + C(q, \dot{q})\dot{q} + g(q) = -K_d\dot{q} + K_p(q - q_d) + g(q)
{{< /katex >}}

By removing the gravity compensation from both sides and making the assumption that in the decentralized controller, the joints are decoupled and the model is completely linearized — thus {{< katex >}} \dot{q}^2 \approx 0 {{< /katex >}} and the Coriolis effect {{< katex >}} C(q, \dot{q})\dot{q} {{< /katex >}} infered to be null.

{{< katex display >}}
B(q)\ddot{q} = -K_d\dot{q} + K_p(q - q_d)
{{< /katex >}}

Obtaining the transfer function of second order using Laplace transform:

{{< katex display >}}
\frac{q(s)}{q_d(s)} = \frac{\frac{K_p}{B}}{s^2 + \frac{K_d}{B}s + \frac{K_p}{B}}
{{< /katex >}}

Comparing the second-order system without dampening and natural frequency with a second-order system with dampening and natural frequency, it is safe to conclude:

{{< katex display >}}
\frac{\omega_n^2}{s^2 + 2 \xi \omega_n s + \omega_n^2} = \frac{\frac{K_p}{B}}{s^2 + \frac{K_d}{B}s + \frac{K_p}{B}}
{{< /katex >}}

{{< katex display >}}
K_p = \omega_n^2 B
{{< /katex >}}

{{< katex display >}}
K_d = 2 \xi \omega_n^2 B
{{< /katex >}}

Hence, `B` will be represented by the maximum values of the mass matrix `B` diagonal. Given that each joint will be controlled separately, each joint will have its respective `Kd`, `Kp` and `B`.

### 9.2. Worst Case Inertia

To identify the worst-case inertia, represented by the maximum values along the diagonal of the `B` matrix, a trial-and-error method was employed by searching for joint values `q`. The obtained values and the corresponding robot configurations for each inertia are shown in the table. Notably, for joints `q1`, `q2` and `q3` the changes in inertia values were negligible, suggesting that any configuration of `q` would yield the maximum inertia value for these joints.

      | Maximum Inertia  | Joint values [\º]  |
| Joint |   [Kg ⋅ m^2]              | q1 | q2 | q3 | q4 | q5 | q6 | q7 |
|-------|---------------------------|----|----|----|----|----|----|----|
| q1    | 89.24                     | -  | -  | -  | -  | -  | -  | -  |
| q2    | 5.319                     | -  | -  | 0  | 10 | -145 | 0  | -  |
| q3    | 4.876                     | -  | -  | -  | 10 | -90 | 0  | -  |
| q4    | 2.516                     | -  | -  | -  | -  | -90 | 0  | -  |
| q5    | 2.111                     | -  | -  | -  | -  | | - [-140,-45] and [45,140] | - |
| q6    | 2.101                     | -  | -  | -  | -  | -  | -  | -  |
| q7    | 2.1                       | -  | -  | -  | -  | -  | -  | -  |


{{< details "**Configurations:** q2, q3 and q5 - (click to expand)" close >}}

`q2` configuration:

![53](https://live.staticflickr.com/65535/53469658316_d7eab0a5b8_w.jpg) 

`q3` configuration:

![54](https://live.staticflickr.com/65535/53469800663_f1ea4ae6a4.jpg)

`q5` configuration:

![55](https://live.staticflickr.com/65535/53469658306_13bc14c536.jpg)

{{< /details >}}


### 9.3. Controller Gains Tuning

## 10. Centralized Controller

## 11. Trajectory Planning 

### 11.1. Trajectory with Decentralized Controller

### 11.2. Trajectory with Centralized Controller