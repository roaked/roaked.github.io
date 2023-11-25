---
title: Drone Controller Design Pt. 2 
weight: 4
---

# **Controller Design Pt. 2**

{{< hint tip >}}
If you wish to skip the theoretical and implementation details, [please click here to jump to the summarized findings Ch. 9 - Piloting in a Loop](https://ricardochin.com/docs/2code/1drone/_index3/#9-piloting-in-a-loop). I have cool images! ✌️{{< /hint >}}

## 1 Continuing from Section 2...

In the final phase of the project, a more practical approach was adopted to tackle the problem. Discrete controllers and observers were designed for the drone, with simulations implemented to test it in a highly realistic environment. This involved using provided tracks and fine-tuning controllers to minimize following errors while maintaining optimal speed without causing critical destabilization.

Section 2 introduced the discrete model of the drone, serving as the foundation for designing subsequent controllers and observers detailed in later sections.

In Section 3.1, the discrete LQR controller was introduced, analyzing its performance concerning step references. Following that, Section 3.2 outlined the design of the MPC, discussing the group's selected parameters and restrictions.

Section 4 delved into the existing measurement noise and its management through the Kalman filter. This section covered the filter's design parameters and the results obtained from its implementation.

Sections 6 and 7 presented the results achieved in the simulator using the provided tracks, evaluating both controllers and models—linear and non-linear. Additionally, pertinent issues addressed during this phase of the project were discussed.

Finally, Section 8 encapsulated the ultimate conclusions drawn from this final project phase, along with remarks regarding the entirety of the project.

## 2 Discrete-time Linearized Model


{{< hint note >}}
As previously indicated, the drone model can be segmented into four subsystems. Among these, the yaw and vertical axis subsystems are second-order.
{{< /hint >}}

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

{{< hint note >}}
The longitudinal and lateral axis subsystems are fourth order.
{{< /hint >}}   

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

{{< hint info >}}
Reachibility ✔️
{{< /hint >}}

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

{{< hint info >}}
Observability ✔️
{{< /hint >}}

## 3 Controller Design

With the verification of reachability and observability properties, the stage is set for designing the necessary controllers and observers. Notably, based on preceding project findings, I opted against designing a controller via pole placement. I will explore two strategies: the discrete linear quadratic regulator (LQR) and the model predictive control (MPC), both extensively discussed and analyzed in subsequent sections.

{{< hint info >}}
It's worth noting that these two controllers employ slightly different design approaches. The `LQR` is specifically tailored for each of the subsystems outlined in the previous section, mirroring the approach taken with the continuous system. This direct strategy aligns with the existence of four independent subsystems and their corresponding references, essential for the drone's tracking, considering that the roll and pitch variables are not directly controlled.

In contrast, the `MPC` considers the entire 12th order discretized system. The group's design parameters necessitated the inclusion of roll and pitch variables in the system output for better drone stabilization. While this could have been achieved by modifying the previously presented subsystems, the [MPC MATLAB toolbox's](https://www.mathworks.com/products/model-predictive-control.html) graphical interface offered a more intuitive and convenient method to design the MPC for the entire system. 
{{< /hint >}}

