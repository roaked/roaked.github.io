---
title: "Robotics: Kin, Dynamics & Control"
weight: 7
---

# KUKA Robotics: From Kinematics to Motion Planning

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

In the case of the KR6, the end effector's position and orientation are described by 6 parameters: three displacements (`x`, `y`, `z`) and three Euler angles (`{{< katex>}}\phi{{</ katex>}}`, `{{< katex}}\theta{{</ katex>}}`, `{{< katex}}\psi{{</ katex>}}`). However, the robot has 7 links, indicating it possesses 7 degrees of freedom. This redundancy implies that, to solve the inverse kinematics problem, one of the joint variables must be specified initially. In this scenario, `q1`, corresponding to the prismatic joint, was chosen to be fixed, leaving 6 degrees of freedom to be determined.

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
\alpha = atan2 (- p _{wy}, \sqrt{p_{wx}^2 + p_{wz}^2} - a_2)
{{< /katex >}}

{{< katex display >}}
\beta = atan2 (a_w \sqrt{1-c_w^2}, a_3 + a_w c_w)
{{< /katex >}}

{{< hint important >}}
After computing `q1`, `q2`, `q3` and `q4` only the wrist parameters are left to find. Hence, using rotation matrix 4 -> 7, it is possible to compute `q5`, `q6` and `q7`
{{< /hint >}}

![15](https://live.staticflickr.com/65535/53469851869_a78896bf27_c.jpg)

The system offers two input options for `pd` and `Rd`:

- Option 1: Users can specify the end effector position and orientation using parameters `x`, `y`, `z` (combined to form `pd`) and the Euler angles `{{< katex>}}\phi{{</ katex>}}`, `{{< katex}}\theta{{</ katex>}}`, `{{< katex}}\psi{{</ katex>}}`. The rotation matrix `Rd` is derived from these Euler angles.

- Option 2: Users can input joint variables `q`, which are then processed and transformed into `pd` and `Rd` through direct kinematics. This option is particularly useful for validation purposes.

To select either Option 1 or Option 2, two manual switches are provided. Both switches should be consistently either up (Option 1) or down (Option 2). Once the option is chosen, `pd` and `Rd` are transformed into `p07` and `R07`.

The inverse kinematics block accepts four inputs: `p07`, `R07`, `q1`, and a vector defining the robot's configuration. The configuration vector should consist of integers 0 or 1, representing:

- 1. The elbow's orientation (1 for up, 0 for down).
- 2. The wrist's s6 value (1 for positive, 0 for negative).
- 3. The robot's orientation (1 for facing forwards, 0 for facing backward).
- 4. An additional number to address out-of-boundary positions when `q1` is fixed. Setting this value to 1 allows `q1` to take the inputted value, while 0 aligns `q1` with `xb` from the end effector position relative to the base reference frame — a "free range" configuration for `q1`.

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

## 8. Newton-Euler Formulation

## 9. Decentralized PID Joint Controllers

### 9.1. Stifness and Damping Gains

### 9.2. Worst Case Inertia

### 9.3. Controller Gains Tuning

## 10. Centralized Controller

## 11. Trajectory Planning 

### 11.1. Trajectory with Decentralized Controller

### 11.2. Trajectory with Centralized Controller