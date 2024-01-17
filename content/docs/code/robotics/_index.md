---
title: "Robotics: Kin, Dynamics & Control"
weight: 7
---

# KUKA: From Kinematics to Motion Planning

[To finalize report when I have time :)](https://github.com/roaked/robotics-kinematics-dynamics-and-control)

![soakd](https://blogs.mathworks.com/images/seth/2009Q2/Robot.gif)

{{< hint warning >}}

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

| Link | di [ m] |𝜗i [ rad] | ai [ m] | α [ rad] | offset [ rad] |
|------|-------------|-----------------------|------------|---------------------|------------------------|
| 1    | d1      | 0                     | 0          | -π/2   | 0                      |
| 2    | 0           | 𝜗2        | 0.025      | π/2   | 0                      |
| 3    | 0           | 𝜗3        | 0.315      | 0                   | 0                      |
| 4    | 0           | 𝜗4        | 0.035      | π/2   | 0                      |
| 5    | -0.365      | 𝜗5       | 0          | -π/2   | 0                      |
| 6    | 0           | 𝜗6        | 0          |π/2    | 0                      |
| 7    | -0.080      | 𝜗7        | 0          | π              | 0                      |



For this robot, the vector of joint variables `q` can be expressed as:

{{< katex display >}}
q = [ d1 \hspace{.2cm} \vartheta_2 \hspace{.2cm} \vartheta_3 \hspace{.2cm} \vartheta_4 \hspace{.2cm} \vartheta_5 \hspace{.2cm} \vartheta_6 \hspace{.2cm} \vartheta_7 ]^T
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

{{< hint warning >}}
To add later...
{{< /hint >}}

## 5. Geometric Jacobian

The geometric Jacobian is a matrix that represents the relationship between joint velocities and the linear and angular velocities of the end effector in a robotic system. It is a crucial tool in robotics for understanding how changes in joint positions contribute to the overall motion of the robot's end effector.

{{< katex display >}}
J = \begin{bmatrix} J_P \\ J_O \end{bmatrix}
{{< /katex >}}

Here,

- {{< katex >}}J_P{{< /katex >}} is a (3 × n) matrix that relates joint velocities to end effector linear velocities.
- {{< katex >}}J_O{{< /katex >}} is a (3 × n) matrix that relates joint velocities to end effector angular velocities.

{{< katex display >}} 
\begin{bmatrix} \dot{p}_e \\
w_e
\end{bmatrix}
= \begin{bmatrix}
JP \\
JO
\end{bmatrix}
q
{{< /katex >}}

The best approach to compute the Jacobian is to separate the linear and angular velocities.

{{< katex display>}}
\dot{p_e} = \sum_{i=1}^{n} \frac{\partial p_e}{\partial q_i} \dot{q_i} = \sum_{i=1}^{n} J_{P_i} \dot{q}_i
{{< /katex >}}


{{< katex display>}}
w_e = \sum_{i=1}^{n} w_{i-1,i} = \sum_{i=1}^{n} J_{O_i} \dot{q}_i
{{< /katex >}}

Every term in the Jacobian matrix signifies the velocity contribution of an individual joint `i` to the end effector when all other joints are held stationary.

1. **Prismatic Joints**:

{{< katex display>}}
J_{P_i} = z_{i-1} \\
J_{O_i} = 0
{{< /katex >}}

2. **Revolute Joints**:


{{< katex display>}}
J_{P_i} = z_{i-1} (p_e - p_{i-1}) \\
J_{O_i} = z_{i-1}
{{< /katex >}}

As mentioned earlier, the computation of the Jacobian is encapsulated within the function [DKin.m](https://github.com/roaked/robotics-kinematics-dynamics-and-control/blob/main/DKin.m). This design proves beneficial as, in the process of calculating direct kinematics, the transformation matrix {{< katex>}}T_0^i{{< /katex >}} is computed for each link. To validate the Jacobian matrix, it is essential to compare the velocities obtained through the differentiation of the end effector position with the velocities obtained by multiplying the Jacobian matrix by the derivative of the joints vector, {{< katex>}}\dot{q}{{< /katex >}}. In essence, the following conditions need verification:

{{< katex display>}}
\dot{p_e} - \sum_{i=1}^{n} J_{P_i} \dot{q}_i = 0
{{< /katex >}}


{{< katex display>}}
w_e - \sum_{i=1}^{n} J_{O_i} \dot{q}_i = 0
{{< /katex >}}

Having the following Simulink model:

![23](https://live.staticflickr.com/65535/53469950705_3cbb296754_c.jpg)

![24](https://live.staticflickr.com/65535/53469950655_04a460ef38_b.jpg)


In the first figure (the left side of the Simulink model), two types of inputs for `q` are available: sliders or trajectory. `q` enters the direct kinematics block and also the animation block `vrsink`. The second figure represents the validation process. For the validation process, sine wave functions were employed as inputs for `q`. This choice ensured that all joints would be moving simultaneously.

### 5.1. Position Validation

![25](https://live.staticflickr.com/65535/53469950660_4cb4f03c74_z.jpg)


The values on the extreme right side represent the difference between velocities. Since the largest absolute number is on the order of {{< katex >}}10^{-8} {{< /katex>}}, it can be concluded that {{< katex >}}J_P {{< /katex>}} is validated, as these values can be considered approximately zero.

### 5.2. Orientation Validation


Following a similar approach, to obtain {{< katex >}}w_e {{< /katex>}}, matrix S is computed:

{{< katex display>}}
S = \dot{R}(t) \times R^T = \begin{bmatrix} 0 & -\omega_z & \omega_y \\ \omega_z & 0 & -\omega_x \\ -\omega_y & \omega_x & 0 \end{bmatrix}
{{< /katex>}}

R(t)  represents the rotation matrix R in this case. S is a skew-symmetric operator, and its symmetric elements represent the components of the angular velocity vector {{< katex>}}w_e{{< /katex>}}. Note:

{{< katex display>}}
w_e = \begin{bmatrix} w_x w_y w_z \end{bmatrix}^T = \begin{bmatrix} S(3, 2) S(1, 3) S(2, 1) \end{bmatrix}^T
{{< /katex>}}

To validate {{< katex>}}J_O{{< /katex>}}, the difference between the angular velocity vector obtained from `S` and the angular velocity obtained from {{< katex>}}J_O{{< /katex>}} times {{< katex>}}\dot{q}{{< /katex>}} must tend to zero. Results in Simulink are shown below, having used the sine waves as inputs for vector `q`:

![27](https://live.staticflickr.com/65535/53469950650_509204d65c_z.jpg)

### 5.3. Kinematic Singularities

## 6. Closed Loop Inverse Kinematics (CLIK)

CLIK allows to solve the inverse kinematics problem with a closed loop control, as the name implies.

![34](https://live.staticflickr.com/65535/53469672813_aacb08df0d_z.jpg)

Given the redundancy of the manipulator, there is flexibility in how {{< katex >}}\dot{q}{{< /katex>}} and {{< katex >}}J^{-1}(q){{< /katex>}} are computed. Initially, the inverse Jacobian {{< katex >}}J^{-1}(q){{< /katex>}} can be substituted with the damped least squares inverse:

{{< katex display>}}
J^* = J^T(JJ^T + k^2I)^{-1}
{{< /katex >}}

The computation of {{< katex >}}\dot{q}{{< /katex>}} is modified to include the stabilization of the null-space:

{{< katex display>}}
\dot{q} = J^* v_e + (I_n - J^*J)\dot{q}_0
{{< /katex>}}

{{< katex >}}\dot{q}_0{{< /katex>}} is given by:

{{< katex display>}}
\dot{q}_0 = k_0 \left(\frac{\partial w(q)}{\partial q}\right)^T
{{< /katex>}}

{{< katex >}}\omega{{< /katex>}}(q) allows to maximize the distance from mechanical joint limits and is computed as:

{{< katex display>}}
w(q) = -\frac{1}{2n} \sum_{i=1}^{n} \left(\frac{q_i - \overline{q_i}}{q_{iM} + q_{im}}\right)^2
{{< /katex>}}

Hence, there are four distinct gains to consider: {{< katex >}}K_P{{< /katex>}} for position, {{< katex >}}K_O{{< /katex>}} for orientation, `k` for the damped least squares inverse and {{< katex >}}k_0{{< /katex>}} for null-space stabilization. It is imperative to fine-tune each of these gains to achieve the desired system behaviour.

Each gain exerts different influences on the system dynamics. For `k`, a low (or zero) value diminishes the damping effect on the damped least squares inverse, potentially leading to elevated initial velocities {{< katex >}}\dot{q}{{< /katex>}}. Conversely, a high gain results in excessive damping, causing the system to take more time to reach the desired position.

Regarding the null-space stabilization gain {{< katex >}}k_0{{< /katex>}}, a low value prolongs the system's convergence time to the desired solution. Conversely, a high value may introduce slight instability, causing the joints to continue moving even after the robot reaches the desired position as it attempts to minimize the distance from the joint limits {{< katex >}}\omega{{< /katex>}}(q). If {{< katex >}}k_0{{< /katex>}} is excessively high, the computational load also becomes extensive.

{{< katex >}}K_P{{< /katex>}} and {{< katex >}}K_O{{< /katex>}} are straightforward. A higher {{< katex >}}K_P{{< /katex>}} facilitates the robot in reaching the desired position more swiftly, while an increased {{< katex >}}K_O{{< /katex>}} enables to reach the desired orientation faster. Lower values result in sluggish robot movement, whereas higher values may induce excessive speed, with the potential of burdening computations. 

The robot imposes constraints on joint velocities {{< katex >}}\dot{q}{{< /katex>}}. For {{< katex >}}\dot{q}_1{{< /katex>}}, the prismatic joint, the maximum attainable speed is 2.5 m/s. Revolute joints, on the other hand, have a maximum speed of around 360º/s, equivalent to 6.28 rad/s.

Another constraint pertains to the range of motion for each joint, encompassing the maximum and minimum angles for revolute joints and the maximum and minimum distances for the prismatic joint. These values were extracted from the robot manual and incorporated into our model during the symbolic expression calculations for {{< katex >}}\dot{q}_0{{< /katex>}}.

![35](https://live.staticflickr.com/65535/53469950615_3fffa19de1_b.jpg)

![36](https://live.staticflickr.com/65535/53469533791_de4c0eaf20.jpg)

### 6.1. Validation

{{< hint warning >}}
To add later...
{{< /hint >}}


## 7. Link Properties

To incorporate the dynamics of the robot, it's essential to determine approximate values for the mechanical properties of the links. Solidworks, with its available CAD models, was employed for this purpose. Assigning a material in Solidworks is a crucial step, and since the CAD models represented solid bodies instead of hollow structures, it was necessary to adjust the density.

Given that the robot's manual specified a weight of 50 kilograms for the KR6 robot without the carriage and KL beam, a uniform density of 1680 kg/m³ was applied in Solidworks. This density adjustment resulted in the KR6 parts weighing approximately 50.4 kilograms, providing a satisfactory approximation. It's noteworthy that link 1 comprises two parts: the base of KR6 and a carriage supporting KR6. In the manual, the carriage weight is not included in the KR6's weight specification.

| Link | Mass [ kg] | x | y | z | Ixx | Iyy | Izz | Ixy | Ixz | Iyz |
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

### 9.1. Stiffness and Damping Gains

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

To identify the worst-case inertia, represented by the maximum values along the diagonal of the `B` matrix, a trial-and-error method was employed by searching for joint values `q`. The obtained values and the corresponding robot configurations for each inertia are shown in the subsequent table. Notably, for joints `q1`, `q2` and `q3` the changes in inertia values were negligible, suggesting that any configuration of `q` would yield the maximum inertia value for these joints.


| Joint |   Maximum Inertia  [Kg ⋅ m^2] | q1 | q2 | q3 | q4 | q5 | q6 | q7 |
|-------|---------------------------|----|----|----|----|----|----|----|
| q1    | 89.24                     | -  | -  | -  | -  | -  | -  | -  |
| q2    | 5.319                     | -  | -  | 0  | 10 | -145 | 0  | -  |
| q3    | 4.876                     | -  | -  | -  | 10 | -90 | 0  | -  |
| q4    | 2.516                     | -  | -  | -  | -  | -90 | 0  | -  |
| q5    | 2.111                     | -  | -  | -  | -  | | - [ -140,-45] and [ 45,140] | - |
| q6    | 2.101                     | -  | -  | -  | -  | -  | -  | -  |
| q7    | 2.1                       | -  | -  | -  | -  | -  | -  | -  |



### 9.3. Controller Gains Tuning

Considering the trial-and-error approach, it was decided to incorporate a simple oval trajectory for the end-effector. The objective is to assess the robot's response to this trajectory and analyze the errors across all joints. The oval trajectory involves the end-effector completing two revolutions. These experiments are conducted over a 20-second duration, enabling the exploration of various natural frequencies and damping ratios.

![56](https://live.staticflickr.com/65535/53469978149_31d94f96f1_w.jpg)

To initiate the process, it was determined to employ identical {{< katex >}}\omega_n{{< /katex>}} (natural frequency) and {{< katex >}}\xi{{< /katex>}} (damping ratio) values for all joints. 

|      | q1  | q2  | q3   | q4   | q5   | q6   | q7   |
|------|-----|-----|------|------|------|------|------|
| wn [ rad/s] | 10  | 10  | 10   | 10   | 10   | 10   | 10   |
| ξ    | 0.707 | 0.707   | 0.707    | 0.707    | 0.707    | 0.707    | 0.707    |
| Kp   | 8924 | 531.9 | 487.6 | 251.6 | 211.1 | 210.1 | 254.1 |
| Kd   | 1262 | 75.2 | 69.0 | 35.6 | 29.9 | 29.7 | 32.7 |
| Maximum Tracking Error | 0.0614 | 0.0953 | 0.1240 | 0.1605 | 0.1805 | 0.1722 | 0.1814 |


![57](https://live.staticflickr.com/65535/53469658291_68abcfbcfd_z.jpg)

Observing the error values, it is evident that they are quite high, with the largest error observed in `q7` at 18 cm. This level of error, even if the robot eventually converges to the final position, is deemed substantial. To address this, the natural frequency of the joints will be increased. This adjustment aims to expedite the robot's response and convergence to the desired values for each joint during the trajectory. Additionally, considering the larger error, a more significant increase in the natural frequency, especially for the last joints, has been decided upon.

|                        | q1   | q2    | q3    | q4    | q5    | q6    | q7    |
|------------------------|------|-------|-------|-------|-------|-------|-------|
| wn [ rad/s]             | 20   | 20    | 20    | 20    | 40    | 40    | 40    |
| ξ    | 0.707 | 0.707   | 0.707    | 0.707    | 0.707    | 0.707    | 0.707    |
| Kp                     | 35696 | 2128  | 1950  | 1006  | 3378  | 3362  | 3360  |
| Kd                     | 2524.1| 150.4 | 137.9 | 71.2  | 119.4 | 118.9 | 118.8 |
| Maximum Tracking Error | 0.0316| 0.0520| 0.0678| 0.0820| 0.0499| 0.0468| 0.0498|

![58](https://live.staticflickr.com/65535/53468756027_2abc8daa83_c.jpg)

As observed, the errors have become significantly smaller, indicating that the increase in natural frequency has accelerated the system's response. It's essential to note that this increase has its limits, as excessively high natural frequencies can render the system unstable. Additionally, with the inertias being relatively small, the stiffness and damping gains become increasingly larger in response. 

|                        | q1   | q2    | q3    | q4    | q5    | q6    | q7    |
|------------------------|------|-------|-------|-------|-------|-------|-------|
| wn [ rad/s]             | 25   | 30    | 40    | 40    | 60    | 60    | 60    |
| ξ    | 0.707 | 0.707   | 0.707    | 0.707    | 0.707    | 0.707    | 0.707    |
| Kp                     | 55775| 4787  | 7802  | 4026  | 7600  | 7564  | 7560  |
| Kd                     | 3155.1| 225.7 | 275.8 | 142.3 | 179.1 | 178.3 | 178.2 |
| Maximum Tracking Error | 0.0257| 0.0363| 0.0416| 0.0435| 0.0353| 0.0331| 0.0353|


![59](https://live.staticflickr.com/65535/53469800638_8fc7d282db_c.jpg)

Now, testing for different damping rations:

| ξ \ Error | q1    | q2    | q3    | q4    | q5    | q6    | q7    |
|------|-------|-------|-------|-------|-------|-------|-------|
| 0.4  | 0.0147| 0.0186| 0.0324| 0.0257| 0.0165| 0.0155| 0.0259|
| 0.707| 0.0257| 0.0363| 0.0416| 0.0435| 0.0353| 0.0331| 0.0353|
| 0.9  | 0.0320| 0.0449| 0.0462| 0.0537| 0.0432| 0.0405| 0.0432|

As evident, a damping ratio of 0.4 results in lower error values, but it introduces more noticeable oscillating behaviour. Further reduction of this value can lead to system instability. Additionally, for values above 0.707, the error tends to increase, and surpassing 1, it causes the system to go unstable. Therefore, a damping ratio of 0.707 will be utilized for the trajectory planning, striking a balance between minimizing error and avoiding excessive oscillations or instability. The finalized parameters for the Stiffness and Damping Gains.

![60](https://live.staticflickr.com/65535/53469658271_2014107e63_c.jpg)
![61](https://live.staticflickr.com/65535/53468756007_c776831f02_c.jpg)

The final objective is to demonstrate the tracking performance of each joint `q` during the oval trajectory. This will serve to affirm that the decentralized control effectively ensures accurate following of the desired `q` values for each joint.

![62](https://live.staticflickr.com/65535/53469658281_ed341505cc_z.jpg)

![63](https://live.staticflickr.com/65535/53469658246_e472495fe9_c.jpg)

{{< hint important >}}
It's crucial to highlight that the selection of natural frequency and damping ratios depends on the trajectory. Lower values for these parameters may generally function, but they might not be sufficient to minimize the tracking error effectively. Adjusting these values based on the characteristics of the specific trajectory is essential for achieving optimal control performance.
{{< /hint >}}


## 10. Centralized Controller

To better manage high velocities with non-linear compensation, a centralized controller has been developed. The centralized controller facilitates joint space inverse dynamics control, enhancing the system's ability to handle complex dynamics and achieve precise control over high-velocity movements.

![64](https://live.staticflickr.com/65535/53470076220_b43ef67e60_c.jpg)

The controller dynamics are:

{{< katex display>}}
\tau = B(q)\ddot{q} + n(q, \dot{q}) \\

n(q, \dot{q}) = \phi(q, \dot{q}) + g(q)
{{< /katex>}}

The block diagram illustrates that the system can be divided into two main components: *stabilizing control* and *non-linear compensation and decoupling*. Within the non-linear part, it is established that:

{{< katex display>}}
y = K_P \tilde{q} + K_D \dot{\tilde{q}} + \ddot{q}_d
{{< /katex>}}

Considering that {{< katex >}} \ddot{\tilde{q}} = \ddot{q} - \ddot{q}_d {{< /katex >}}, replacing y with {{< katex >}}\ddot{q}_d{{< /katex >}}:

{{< katex display>}}
\ddot{\tilde{q}} = K_D \dot{\tilde{q}} + K_P \tilde{q} = 0
{{< /katex>}}

This equation represents a second-order differential equation. The values for Kp (proportional gain) and Kd (derivative gain) are defined from the characteristic equation as follows:

{{< katex display>}}
K_P = \text{diag}\{\omega_{n1}^2, \ldots, \omega_{nn}^2\} \\
K_D = \text{diag}\{2\xi_1\omega_{n1}, \ldots, 2\xi_n\omega_{nn}\}
{{< /katex>}}

**Simulink design:**

![65](https://live.staticflickr.com/65535/53469658241_da2c684048_c.jpg)

For the centralized controller, the parameters `B` and `n` are estimated. In order to thoroughly test the controller and simulate a real-life scenario where the manipulator dynamics may not precisely match the modeled dynamics (due to simplifications and neglecting factors such as viscous forces), a different dynamics block was introduced. This new dynamics block incorporates a 10% error in the masses of the links. While this adjustment might lead to a slightly less optimal controller performance, it enhances the realism of the simulation. Essentially, it is assumed that the positions and velocities of the manipulator joints can be measured, but directly extracting the `B` and `n` parameters requires an estimator due to potential uncertainties in the dynamics.

### 10.1. Controller Gains Tuning

For the centralized controller, the natural frequencies and damping coefficient ratios were initially uniform across all joints. The initial test involved a step response with {{< katex >}}\omega_n = 10, \xi = \frac{\sqrt{2}}{2} \approx{{< /katex >}} 0.707. However, it was observed that the system was underdamped, with some oscillations appearing in the response. To address this, {{< katex >}}\xi{{< /katex >}} was adjusted to 1.

![66](https://live.staticflickr.com/65535/53470076215_fc43a60a45_c.jpg)

Further increasing {{< katex >}}\xi{{< /katex >}} would result in an overdamped system, thereby slowing down the response. Thus, {{< katex >}}\xi{{< /katex >}} was maintained. It is observed that, for both responses, the steady-state error is zero.

Regarding {{< katex >}}\omega_n{{< /katex >}}, since the centralized controller receives desired velocities and accelerations, it doesn't necessitate as high a {{< katex >}}\omega_n{{< /katex >}} as in decentralized control. Elevating {{< katex >}}\omega_n{{< /katex >}} would accelerate the response, potentially leading to motor saturation, while reducing it could slow down the response, increasing the tracking error. To enhance the response speed without causing saturation, {{< katex >}}\omega_n{{< /katex >}} was increased to 15.

![67](https://live.staticflickr.com/65535/53469800598_2905dedb8c_c.jpg)

This image illustrates that the response is rapid, with no overshoot and no steady-state error for all joints. With these parameters, the gains of the controller are {{< katex >}}K_p{{< /katex >}} = {{< katex >}}\omega_n^2 = 225{{< /katex >}} and {{< katex >}}2 \xi \omega_n = 30{{< /katex >}}. Another conducted test involved a ramp response, where imposing a ramp with a slope of 1 yielded the following tracking errors:

![68](https://live.staticflickr.com/65535/53469978089_4640e1ca09_z.jpg)

From the figure above, it is evident that the system rapidly converges to a tracking error of zero. This rapid convergence is attributed to the fact that desired velocities of the joints are provided. To further validate the centralized controller, it was tested with a simple oval trajectory, yielding the following results:

![69](https://live.staticflickr.com/65535/53469800593_1892f9b62a_c.jpg)
![70](https://live.staticflickr.com/65535/53469658226_1e46402deb.jpg)


## 11. Trajectory Planning 

For trajectory planning, the decision was made to have the KR6 end-effector trace the ["IST"](https://tecnico.ulisboa.pt/) university logo. Achieving this required the development of a method for the robot to follow the designated trajectory. To implement the trajectory, a time-law was formulated, allowing for the specification of the time duration of a trajectory and establishing boundary conditions for the movement.

The chosen time-law is based on a cubic polynomial. The selected constraints include initial and final times to coincide with the initial and final position of the trajectory, and initial and final accelerations set to zero. These conditions contribute to a seamless and smooth movement of the robot. The resulting time-law is:

{{< katex display >}}
s = s_0 + 3(sf - s_0)(\frac{t}{t_f})^2 - 2(sf - s_0)(\frac{t}{t_f})^3
{{< /katex >}}

{{< katex display >}}
\frac{ds}{dt} = \frac{6}{t_f^2}(s_f - s_0)t - \frac{6}{t_f^3}(s_f - s_0)t^2
{{< /katex >}}


The trajectory was implemented using the parameterization of the length as a function of time, denoted as s = s(t). The trajectory was carefully designed to enable the robot to write the letters without abrupt changes in velocities, aiming to avoid generating very large accelerations. Specifically, the goal was to eliminate discontinuities in velocities. For instance, when forming the letter 'T', at some point, the end-effector would need to reverse its direction. Instead of an abrupt reversal from positive to negative velocity, the robot first slows down until it reaches zero velocity and then begins moving backward. Similarly, sharp 90º turns were avoided.

The trajectory was integrated into the Closed Loop Inverse Kinematics from the initial part of the project. The Simulink subsystem for the trajectory is depicted below:

![71](https://live.staticflickr.com/65535/53468755942_111edcf19b_c.jpg)

After executing CLIK, the obtained results for the position of the end-effector were visualized by plotting the data in Matlab.

![72](https://live.staticflickr.com/65535/53469978084_8b2e5dd09b_w.jpg)

Since CLIK had already been validated in the previous report, the successful implementation of the trajectory demonstrates that the system is performing as intended. The trajectory was designed to last for 20 seconds, featuring letters with dimensions of 0.4 x 0.4 meters. The corners in the letters 'I' and 'T' were intentionally rounded to achieve a smoother movement.

When executing CLIK to obtain the desired positions, velocities, and accelerations, it is crucial to adjust the solver settings. By default, the solver has a variable step. Switching to a fixed step, with a step size of 0.01 seconds, allows for a more precise sampling of data from CLIK.

### 11.1. Trajectory with Decentralized Controller

After attempting to use the previously determined gain values for the new trajectory, an unstable behavior was observed in the error values for joint `q1`. Consequently, the natural frequency for q1({{< katex >}}\omega_{n1}{{< /katex >}}) was reduced by 5 rad/s to {{< katex >}}\omega_{n1} = 20{{< /katex >}}. This adjustment yielded the following error values for the "IST" trajectory.

|      | q1    | q2    | q3    | q4    | q5    | q6    | q7    |
|------|-------|-------|-------|-------|-------|-------|-------|
| Maximum Error | 0.0556 | 0.0408 | 0.0339 | 0.0183 | 0.0574 | 0.0214 | 0.1417 |

![73](https://live.staticflickr.com/65535/53469658201_1b666e68dc_z.jpg)

The elevated error value for joint `q7` can be attributed primarily to the controller relying solely on joint position. Since the end-effector exhibits varying velocities along the path, especially during curves where velocities are higher, it leads to the controller "losing track" of the position, resulting in larger errors. However, the animation remains satisfactory as the robot remains stable throughout, and the error converges to the final value. Another evaluation approach is to examine the different end-effector positions throughout the trajectory. 

![74](https://live.staticflickr.com/65535/53469800553_e16e234471_w.jpg)

From this plot, as mentioned earlier, it is evident that the robot encounters difficulty in accurately following the curves of the trajectory, particularly in the contours of the "I" and "T" where velocities are higher. The most significant tracking error occurs during the "S" trajectory, characterized by two consecutive curves. The following figures will illustrate the desired positions, current positions of the robot, and the error between them.

![75](https://live.staticflickr.com/65535/53470076170_ffa5b4255b_c.jpg)

![76](https://live.staticflickr.com/65535/53469978069_ac9cc8824c_c.jpg)

From the last two images, it may appear that the values on the y-axis in the first figure exhibit the most disparity, but this is solely due to the scale of the Scope. The highest difference error in this axis is approximately 0.00037 meters.

Given that the trajectory primarily occurs in the "x" and "z" axes, these are the dimensions that display the highest peaks in error. From the right subplot of the last image, five prominent spikes are observed in the x-axis, corresponding to the five main curves of the trajectory (two for "I," two for "S," and one for "T"). The maximum error values obtained for x, y, and z are {{< katex >}}e_x = 0.0429, e_y = 0.0011 \text{and} e_z = 0.0119  {{< /katex >}}

![77](https://live.staticflickr.com/65535/53469800528_e09e68643b_z.jpg)


The plots illustrate that the joint values `q` exhibit capacity of following the trajectory throughout its course, showcasing the effectiveness of the decentralized controller as a capable tool for controlling robot trajectories.

### 11.2. Trajectory with Centralized Controller

When using {{< katex >}}\xi = 1 \text{and} \omega_n = 15{{< /katex >}}, the following results were obtained after executing the "IST" trajectory:

|      | q1    | q2    | q3    | q4    | q5    | q6    | q7    |
|------|-------|-------|-------|-------|-------|-------|-------|
| Maximum Error | 0.0024 | 0.0110 | 0.0061 | 0.0027 | 0.0379 | 0.0019 | 0.0966 |

![78](https://live.staticflickr.com/65535/53469978034_0994d50615.jpg)


Similar to the Decentralized controller, the most significant error occurs with joint `q7`. The results are not significantly different from the decentralized control, with small errors. The end-effector position plot yielded the following result:

![79](https://live.staticflickr.com/65535/53469978039_a2c83d9bea_w.jpg)

The most substantial errors emerge in the tight turns of the "I" and "T," as well as the large curves during the "S." However, these errors are not overly significant, as the plots closely resemble each other, and the letters can still be distinctly recognized. As for the errors in the end-effector position:

![80](https://live.staticflickr.com/65535/53469800513_817f2c893b_z.jpg)

The maximum values of error obtained for x, y, and z are {{< katex >}}e_x = 0.0045, e_y = 0.0017 \text{and} e_z = 0.0030  {{< /katex >}}. As for evolution of the joints’ positions:

![81](https://live.staticflickr.com/65535/53469978019_0556179347_c.jpg)

The plots of `q` and `qd` are nearly overlaid, indicating good tracking. The most noticeable errors are observed in `q5` and `q7`, as expected from the table of maximum errors presented.

As a final experiment, we decided to increase the values of {{< katex >}}\omega_n{{< /katex >}} to observe their impact on the tracking errors. The chosen values were {{< katex >}}\omega_n = [ 20,20,20,20,20,30,20,50]{{< /katex >}}. {{< katex >}}\omega_{n5}{{< /katex >}} and {{< katex >}}\omega_{n7}{{< /katex >}} were selected to have higher values than the rest due to having the largest errors. The results were:

|      | q1    | q2    | q3    | q4    | q5    | q6    | q7    |
|------|-------|-------|-------|-------|-------|-------|-------|
| Maximum Error | 0.0024 | 0.0081 | 0.0035 | 0.0018 | 0.0222 | 0.0020 | 0.0286 |


![82](https://live.staticflickr.com/65535/53469978009_ec14068be9_c.jpg)

The errors are smaller, and the "spikes" persist for a shorter duration than before (indicating a faster rise and fall). Upon closer inspection of a zoomed-in section, it appears that the system remains stable, with no significant oscillations:

![83](https://live.staticflickr.com/65535/53469658161_31bd7c8b44_c.jpg)

These values produced slightly better results than those used before, but they are more tuned to this specific trajectory. The previous values also yielded good results and may be more appropriate for a broader range of trajectories, with less risk of causing instability in the system.

## 12. Final Remarks

At the first chapters, the direct kinematics and inverse kinematics were implemented. Although direct kinematics where the joint coordinates are given to determine the end effector's position and orientation, were successfully implemented and validated, the inverse kinematics posed a challenge and a geometric approach was employed, involving drawings to enhance understanding of joint relationships. It was found that `q1` needed to be initially prescribed as a solution. 

The CLIK model overcame the redundancy in the robot, providing solutions without prescribing q1. However, it introduced complexities with four different gains, requiring tuning for optimal performance. While CLIK simplified the algebraic work compared to the non-closed-loop form, the gain tuning process was not trivial, and finding an optimal solution required adjusting multiple parameters.

Despite the complexities, CLIK offered a more straightforward approach to inverse kinematics, especially for robots with different architectures. The ability to handle redundancy was a significant advantage. After tuning the gains to optimal values, CLIK demonstrated reliable results.

{{< hint important >}}
It's important to note that, while a geometric approach was chosen for the inverse kinematics, other analytical approaches utilizing transformation matrices for each joint are possible. The geometric approach, driven by its intuitiveness, was selected for this project.
{{< /hint >}}

As for the dynamics and control: starting with the extraction of masses and inertias of the links, SolidWorks proved to be a successful tool. The choice of Newton-Euler formulation for dynamics computation was efficient due to its recursive nature and simple implementation, making it easier and more effective than Lagrange. The fall of the robot under gravity when no torques were applied aligned with expectations.

In terms of controllers, both the Decentralized and Centralized Controllers exhibited good performance in trajectory tracking. However, the Centralized Controller demonstrated superiority, utilizing not only joint positions `qd` but also joint velocities `qd/dt` and accelerations `q2d/dt2` for improved tracking. The Decentralized Controller, employing a P-D control with gravity compensation replacing the integrative part of PID, showed a slight deviation in the reference trajectory, especially during high-velocity movements.

The Centralized Controller, when appropriately tuned, showcased robust performance, even in the presence of disturbances in the estimation of dynamics. The trajectory planning using CLIK was successful, emphasizing its reliability for both kinematic analysis and task description.

The error analysis highlighted the Centralized Controller's superior results over the Decentralized Controller:

|                     | q1     | q2     | q3     | q4     | q5     | q6     | q7     |
|---------------------|--------|--------|--------|--------|--------|--------|--------|
| Decentralized Controller | 0.0556 | 0.0408 | 0.0339 | 0.0183 | 0.0574 | 0.0214 | 0.1417 |
| Centralized Controller   | 0.0024 | 0.0110 | 0.0061 | 0.0027 | 0.0379 | 0.0019 | 0.0966 |


In summary, both controllers proved effective for the imposed trajectory, but for tasks involving high velocities, the Centralized Controller demonstrated greater reliability compared to the Decentralized Controller.