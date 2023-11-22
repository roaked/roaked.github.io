---
weight: 4
bookHidden: false
title: "Finite Element Model Formulation"
---

# 1 Mathematical Background

## 1.1 Partial Differential Equations (PDEs)

In engineering and physics, problems are often described by partial differential equations (PDEs). The "strong form" refers to the original differential equation that must hold exactly throughout the domain, including both the governing equation and the boundary conditions.

However, there are cases where directly solving the strong form might be challenging due to complexities in the equation, irregular geometries, or varying boundary conditions. This is where the "weak formulation" or ("weak form") becomes advantageous.

The weak form introduces a relaxation of the constraints imposed by the strong form by multiplying the governing equation with a weight function (typically a test function) and integrating it over the domain. This relaxation allows for more flexibility in solution techniques and offers several advantages:

- Handling Boundary Conditions: The weak form often simplifies the imposition of different types of boundary conditions.

- Solvability: Some differential equations in the strong form might not have exact solutions or might be difficult to solve directly. The weak form can lead to a more manageable equation for numerical methods.

- Adaptability to Numerical Methods: Finite element, boundary element, and other numerical methods are often more easily applied to weak formulations due to the integral nature of the equations.

- Handling Discontinuities: In problems involving discontinuities or singularities, the weak form can handle these cases more effectively.

- Minimization of Requirements: In some cases, the requirement for differentiability or the number of times an equation needs to be differentiated is reduced, easing computational complexity.

Overall, the weak form is favoured in many situations because it relaxes the strictness of the original problem while maintaining the essential properties required for solutions, making it more adaptable for various solution techniques, especially in numerical analysis.

## 1.2 Strong Form

Let's begin by establishing the strong formulation of the problem, which serves as the foundation of our problem. In this formulation, we encapsulate the fundamental differential equation governing the system's behaviour. This equation, along with the precise boundary conditions and initial values, constitutes the direct representation of the underlying physical laws guiding the system's dynamics.

{{< katex display >}}
\theta - \nabla \cdot (k \nabla u) = f
{{< /katex >}}

For the torsional loading problem k = 1 and f = 2. The following equations indulge:

{{< katex display >}}
\frac{\partial^2 \phi}{\partial x^2} + \frac{\partial^2 \phi}{\partial y^2} = - 2G \theta
{{< /katex >}}

This is referring to the Prandtl stress function, which defines shear stresses as:

{{< katex display >}}
\tau_{xz} = \frac{\partial \phi}{\partial y} \text{    and    } \tau_{yz} = \frac{\partial \phi}{\partial x}
{{< /katex >}}

The resolution of a torsion problem involves solving the Prandtl equation by imposing the boundary condition around the entire perimeter of the figure, as is known:

{{< katex display >}}
\phi = 0
{{< /katex >}}

To do this, the Prandtl function is divided by {{< katex >}}G\theta{{< /katex >}}, considering this value is not known, as follows in the next set of expressions:

{{< katex display >}}
\frac{\partial^2 \psi}{\partial x^2} + \frac{\partial^2 \psi}{\partial y^2} = - 2 \text{    and    } \psi = \frac{\phi}{G \theta}
{{< /katex >}}


{{< katex display >}}
\tau_{yz} = -G \theta \frac{\partial \psi}{\partial x} \text{    and    } \tau_{xz} = G \theta \frac{\partial \psi}{\partial y}
{{< /katex >}}

The developed program will calculate the solution given by {{< katex >}}\psi{{< /katex >}} for each node, the shear stresses, and the value of torsional rigidity. The problem of {{< katex >}}G \theta{{< /katex >}} is solved using the formula:

{{< katex display >}}
G \theta = \frac{Mt}{J}
{{< /katex >}}

In the previous formula, the M (torsional moment) and t (thickness) take the value of 1.

## 1.3 Weak Form

The weak form enables transforming the differential equation into an integral equation, where the initial approach involves integrating the equation across the entire domain. Rules are applied to obtain the weak form, namely, defining the residual by multiplying it with an arbitrary function, integrating by parts the highest-order term, and rearranging the equation. Thus:

{{< katex display >}}
\iint_S \frac{\partial \phi_i}{\partial x} \frac{\partial \phi_j}{\partial x} + \frac{\partial \phi_i}{\partial y} \frac{\partial \phi_j}{\partial y} dA = \iint_S 2\phi_i dA
{{< /katex >}}

The stiffnex matrix is given by:

{{< katex display >}}
K_{ij} = \iint_S \frac{\partial \phi_i}{\partial x} \frac{\partial \phi_j}{\partial x} + \frac{\partial \phi_i}{\partial y} \frac{\partial \phi_j}{\partial y} dx dy
{{< /katex >}}

Additionally, the load vector is presented as:

{{< katex display >}}
F_i = \iint_S 2 \phi_i dx dy
{{< /katex >}}

In the considered problem, the only essential boundary condition will be the null solution across the entire perimeter of the figure, accompanied by a distributed load of magnitude 2 applied across the entire mesh.

## 1.4 Analytical Integration

Simplifying the problem's resolution, only regular 4-node elements with 1 degree of freedom will be considered. Information from the book ["Introduction to the Finite Element Method (3rd edition)" by J.N. Reddy](https://books.google.at/books/about/An_Introduction_to_the_Finite_Element_Me.html?id=8gqnRwAACAAJ&redir_esc=y) provides the following details:

- Stiffness matrix (e): {{< katex >}}K^e = \frac{k}{6ab}  \begin{bmatrix}
2(a^2+b^2) & a^2-2b^2 & -(a^2+b^2) & -2a^2+b^2\\
a^2-2b^2 & 2(a^2+b^2) & -2a^2+b^2 & -(a^2+b^2)\\
-(a^2+b^2) & -2a^2+b^2 & 2(a^2+b^2) & a^2-2b^2\\
-2a^2+b^2 & -(a^2+b^2) & a^2-2b^2 & 2(a^2+b^2)\\
\end{bmatrix}   {{< /katex >}}

- Load vector (e): {{< katex >}}f^e =\frac{fab}{4} \begin{bmatrix}
1\\
1\\
1\\
1\\
\end{bmatrix}   {{< /katex >}}

Where 'a' corresponds to the length of the base of the element, and 'b' corresponds to the height of the element.

## 1.5 Numerical Integration

These functions aim to calculate the elemental stiffness matrix using the Gauss-Jordan approximation, which simplifies integration to a summation. To achieve this, a coordinate transformation from {{< katex >}}(x,y){{< /katex >}} to {{< katex >}} (\xi, \eta) {{< /katex >}} is applied, while also computing the Jacobian of this transformation. Subsequently, the rules of Gauss points are employed.

In Gauss integration, the stiffness matrix K is computed through reduced integrals:

{{< katex display >}}
K_{ij} = \frac{\partial \phi_i}{\partial x} \frac{\partial \phi_j}{\partial x} + \frac{\partial \phi_i}{\partial y} \frac{\partial \phi_j}{\partial y} \times J \times w
{{< /katex >}}

{{< katex display >}}
J = \begin{bmatrix}
\frac{\partial x}{\partial \xi} & \frac{\partial x}{\partial \eta}\\
\frac{\partial y}{\partial \xi} & \frac{\partial y}{\partial \eta}\\
\end{bmatrix}  = \frac{ab}{4}
{{< /katex >}}  

In the previous Gauss integration, 'w' corresponds to the integration weights. For a 1x1 integration, the integration is performed solely at the center of the element, with a weight of w = 1. For 2x1 or 1x2 integration, each 'w' value represents the sum of 2 reduced integrals (2 different points within the element), and the weight is w=2. For a 2x2 integration, the summation includes 4 reduced integrals (4 different points), and w=1. The following values of {{< katex >}}\overline{x}{{< /katex >}} and {{< katex >}}\overline{y}{{< /katex >}} correspond to the Gauss points:

{{< katex display >}}\phi_1 = (1-\frac{\overline{y}}{b})(1-\frac{\overline{x}}{a}) \quad \quad \phi_2 = \frac{\overline{x}}{a}(1-\frac{\overline{y}}{b}){{< /katex >}}

{{< katex display >}}\phi_3 = \frac{\overline{y}}{b}\frac{\overline{x}}{a} \quad \quad \phi_4 = \frac{\overline{y}}{b}(1-\frac{\overline{x}}{a}){{< /katex >}}   

Consequently:

{{< katex display >}} \text{1x1 Rule} : \overline{x}=\frac{a}{2} \quad \text{and} \quad \overline{y}=\frac{b}{2}
{{< /katex >}}

{{< katex display >}} \text{2x2 Rule} : \overline{x_{1,2}}=\frac{a}{2}(1 \pm \sqrt{\frac{1}{3}}) \quad \text{and} \quad \overline{y_{1,2}}=\frac{b}{2}(1 \pm \sqrt{\frac{1}{3}})
{{< /katex >}}  

## 1.6 Torsional Constant 

After calculating the nodal solution, the torsion constant J can be determined by:

{{< katex display >}}
J^e = \int_0^b \int_0^a 2 \psi^e dxdy = \frac{ab}{2}(\psi_1 + \psi_2 + \psi_3 + \psi_4) \qquad J = \sum_i J_i^e
{{< /katex >}}  

## 1.7 Shear Stresses

{{< katex display >}}
\tau_{yz} =  -G \theta \frac{\partial \psi}{\partial x} = -G \theta (\frac{\partial \phi_1}{\partial \overline{x}}\psi_1 + \frac{\partial \phi_2}{\partial \overline{x}}\psi_2 + \frac{\partial \phi_3}{\partial \overline{x}}\psi_3 + \frac{\partial \phi_4}{\partial \overline{x}}\psi_4)
{{< /katex >}}  

{{< katex display >}}
\tau_{xz} =  G \theta \frac{\partial \psi}{\partial y} = G \theta (\frac{\partial \phi_1}{\partial \overline{y}}\psi_1 + \frac{\partial \phi_2}{\partial \overline{y}}\psi_2 + \frac{\partial \phi_3}{\partial \overline{y}}\psi_3 + \frac{\partial \phi_4}{\partial \overline{y}}\psi_4)
{{< /katex >}} 

{{< katex display >}}
\tau_{xy} = \sqrt{\tau_{yz}^2 +\tau_{xz}^2}
{{< /katex >}} 

# 2 Time for some coding!

## 2.1 READ_ME

The program starts by executing the script ["MainG10.m"](https://github.com/roaked/fem-math-formulation/tree/main/code) **directly available on my github page** and should contain all the subfunctions and their respective 'txt' files in the respective folder. Once the program is initiated, the user inputs the data file name in the form of "nome.txt." The data file should contain the node coordinates, connectivity matrix, and boundary conditions. The data file created for this problem is named ["dadosg10.txt"](https://github.com/roaked/fem-math-formulation/tree/main/code).

After submitting the data file, the program reads the file and automatically opens a figure displaying the mesh with numbered nodes and elements. Simultaneously, a menu with various options is displayed. The user should first choose the type of integration and then select the desired plots for shear stresses, stress contour lines, and nodal solutions. Submitting the options is done by entering the corresponding number. The program is terminated by entering the number 0.

Upon program completion, a file is created containing the data obtained from the last integration performed.


## 2.2 Validation Based on Book

The developed program has been tested for a known problem described in the book ["Introduction to the Finite Element Method (3rd edition)" by J.N. Reddy](https://books.google.at/books/about/An_Introduction_to_the_Finite_Element_Me.html?id=8gqnRwAACAAJ&redir_esc=y) - **Example 8.5.6**. In this example, torsion of a square section bar was considered. As the problem is axisymmetric, nodal solution and shear stresses were calculated for a quarter of the square. The following results were obtained at the nodes for the nodal solution, using a 4x4 mesh:

{{< katex display >}}
\begin{bmatrix}
x & y & \text{Analytical \\ Integration} & \text{Gauss 2x2} & \text{Gauss 1x2} & \text{Gauss 1x1} & \text{Reddy} \\
0.0000 & 0.0000 & 0.1492 & 0.1492 & 0.1501 & 0.1513 & 0.1492 \\
0.1250 & 0.0000 & 0.1412 & 0.1412 & 0.1421 & 0.1429 & 0.1412 \\
0.2500 & 0.0000 & 0.1161 & 0.1161 & 0.1168 & 0.1176 & 0.1161 \\
0.3750 & 0.0000 & 0.0707 & 0.0707 & 0.0711 & 0.0714 & 0.0707 \\
0.1250 & 0.2500 & 0.1103 & 0.1103 & 0.1110 & 0.1116 & 0.1103 \\
0.2500 & 0.2500 & 0.0919 & 0.0919 & 0.0926 & 0.0935 & 0.0919 \\
0.3750 & 0.2500 & 0.0573 & 0.0573 & 0.0577 & 0.0580 & 0.0573 \\
\end{bmatrix}
{{< /katex >}}


The following results were obtained for {{< katex >}}\bar{\tau_{yz}} = \frac{\tau_{yz}}{G\theta} = \tau_{yz} \times J{{< /katex >}} for 4x4 mesh:

{{< katex display >}}
\begin{bmatrix}
x & y & \text{Analytical \\Integration} & \text{Gauss 2x2} & \text{Gauss 1x2} & \text{Gauss 1x1} & \text{Known Analytical \\ Solution} \\
0.06250 & 0.0625 & 0.0618 & 0.0618 & 0.0621 & 0.0625 & 0.0618 \\
0.1875 & 0.0625 & 0.1942 & 0.1942 & 0.1955 & 0.1970 & 0.1939 \\
0.3125 & 0.0625 & 0.3529 & 0.3529 & 0.3553 & 0.3577 & 0.3516 \\
0.4375 & 0.0625 & 0.5528 & 0.5528 & 0.5560 & 0.5593 & 0.5504 \\
\end{bmatrix}
{{< /katex >}}

It's observed that stiffness matrices obtained through analytical integration or Gauss 2x2 are closely aligned with the known values. In fact, the values from analytical integration and Gauss 2x2 are identical when approximated to 4 decimal places. The fewer points used in the Gauss integration, the greater the error, with Gauss 1x1 showing the largest discrepancy between obtained and known values. The largest relative error found in analytical integration and Gauss 2x2 was 0.436%. For Gauss 1x2 integration, the largest relative error was 1.05%, while Gauss 1x1 integration showed the largest relative error of 1.73%. Overall, the values are close to the known ones, indicating that the developed program is functioning correctly.

# 3 Comparison with FEM commercial software Siemens NX

In addition, the problem was tackled by resorting to the commercial software NX, which unfortunately lacks a dedicated solver for torsion problems. As a workaround, the issue was addressed using the *Sol 153 solver – Steady State Nonlinear Heat Transfer*. This approach was selected due to the analogy between torsion and heat problems. Specific conditions were applied, including setting a boundary temperature at T = 0°C, introducing a uniform heat generation of 2 W/m³ throughout the section, and assigning a thermal conductivity value of K = 1 to the material. To compare results, the Prandtl function was solved using different integration methods in Matlab against the outcomes obtained through NX.


The data from the upcoming tables indicates that the minimum error occurs in the analytical integration type and in the numerical integration type with 2x2 Gauss points. As observed in the known problem, the error values are identical for these two integration types. It's worth noting that in the 1x1 Gauss points integration, there is a considerable relative error percentage (15.2%), which decreases in the case of 2x1 (8.6%) and 1x2 Gauss points (4.84%)

## 3.1 Analytical Integration

{{< details "**Results from Analytical Integration:** - (click to expand)" close >}}

**Maximum error devation**: 3,99501E-06 %

\begin{bmatrix}
\text{Node} & \text{Analytical Solution Matlab (m)} & \text{Siemens NX (m)} & \text{Relative Error (%)} & \text{Node} & \text{Analytical Solution Matlab (m)} & \text{Siemens NX (m)} & \text{Relative Error (%)} \\
1 & 0 & 0 & 0 & 49 & 0 & 0 & 0 \\
2 & 0 & 0 & 0 & 50 & 1.52894765E-03 & 1.52894761E-03 & 2.07246E-06 \\
3 & 0 & 0 & 0 & 51 & 2.25918639E-03 & 2.25918647E-03 & 3.61502E-06 \\
4 & 0 & 0 & 0 & 52 & 2.22762401E-03 & 2.22762395E-03 & 2.75076E-06 \\
5 & 0 & 0 & 0 & 53 & 1.48307979E-03 & 1.48307974E-03 & 3.20316E-06 \\
6 & 0 & 0 & 0 & 54 & 0 & 0 & 0 \\
7 & 0 & 0 & 0 & 55 & 0 & 0 & 0 \\
8 & 0 & 0 & 0 & 56 & 0 & 0 & 0 \\
9 & 0 & 0 & 0 & 57 & 0 & 0 & 0 \\
10 & 0 & 0 & 0 & 58 & 0 & 0 & 0 \\
11 & 0 & 0 & 0 & 59 & 0 & 0 & 0 \\
12 & 0 & 0 & 0 & 60 & 0 & 0 & 0 \\
13 & 0 & 0 & 0 & 61 & 0 & 0 & 0 \\
14 & 0 & 0 & 0 & 62 & 0 & 0 & 0 \\
15 & 0 & 0 & 0 & 63 & 0 & 0 & 0 \\
16 & 7.19690280E-04 & 7.19690288E-04 & 1.08731E-06 & 64 & 1.60173557E-03 & 1.60173560E-03 & 1.30914E-06 \\
17 & 8.63924007E-04 & 8.63923982E-04 & 2.91831E-06 & 65 & 2.18252517E-03 & 2.18252512E-03 & 2.49479E-06 \\
18 & 8.93021588E-04 & 8.93021584E-04 & 4.38814E-07 & 66 & 2.11690066E-03 & 2.11690064E-03 & 1.01038E-06 \\
19 & 8.99847643E-04 & 8.99847655E-04 & 1.34474E-06 & 67 & 1.40940987E-03 & 1.40940992E-03 & 3.31030E-06 \\
20 & 9.05049620E-04 & 9.05049616E-04 & 5.18946E-07 & 68 & 0 & 0 & 0 \\
21 & 9.22107228E-04 & 9.22107254E-04 & 2.87960E-06 & 69 & 0 & 0 & 0 \\
22 & 9.91068761E-04 & 9.91068780E-04 & 1.99588E-06 & 70 & 7.19690280E-04 & 7.19690288E-04 & 1.08731E-06 \\
23 & 1.27384391E-03 & 1.27384393E-03 & 1.55619E-06 & 71 & 8.63924007E-04 & 8.63923982E-04 & 2.91831E-06 \\
24 & 1.44339051E-03 & 1.44339050E-03 & 8.95264E-07 & 72 & 8.93021588E-04 & 8.93021584E-04 & 4.38814E-07 \\
25 & 1.64555815E-03 & 1.64555816E-03 & 1.13774E-06 & 73 & 8.99847643E-04 & 8.99847655E-04 & 1.34474E-06 \\
26 & 1.55521043E-03 & 1.55521045E-03 & 8.87392E-07 & 74 & 9.05049620E-04 & 9.05049616E-04 & 5.18946E-07 \\
27 & 1.07160366E-03 & 1.07160362E-03 & 3.99501E-06 & 75 & 9.22107228E-04 & 9.22107254E-04 & 2.87960E-06 \\
28 & 0 & 0 & 0 & 76 & 9.91068761E-04 & 9.91068780E-04 & 1.99588E-06 \\
29 & 0 & 0 & 0 & 77 & 1.27384391E-03 & 1.27384393E-03 & 1.55619E-06 \\
30 & 0 & 0 & 0 & 78 & 1.44339051E-03 & 1.44339050E-03 & 8.95264E-07 \\
31 & 0 & 0 & 0 & 79 & 1.64555815E-03 & 1.64555816E-03 & 1.13774E-06 \\
32 & 0 & 0 & 0 & 80 & 1.55521043E-03 & 1.55521045E-03 & 8.87392E-07 \\
33 & 0 & 0 & 0 & 81 & 1.07160366E-03 & 1.07160362E-03 & 3.99501E-06 \\
34 & 0 & 0 & 0 & 82 & 0 & 0 & 0 \\
35 & 0 & 0 & 0 & 83 & 0 & 0 & 0 \\
36 & 0 & 0 & 0 & 84 & 0 & 0 & 0 \\
37 & 0 & 0 & 0 & 85 & 0 & 0 & 0 \\
38 & 1.60173557E-03 & 1.60173560E-03 & 1.30914E-06 & 86 & 0 & 0 & 0 \\
39 & 2.18252517E-03 & 2.18252512E-03 & 2.49479E-06 & 87 & 0 & 0 & 0 \\
40 & 2.11690066E-03 & 2.11690064E-03 & 1.01038E-06 & 88 & 0 & 0 & 0 \\
41 & 1.40940987E-03 & 1.40940992E-03 & 3.31030E-06 & 89 & 0 & 0 & 0 \\
42 & 0 & 0 & 0 & 90 & 0 & 0 & 0 \\
43 & 0 & 0 & 0 & 91 & 0 & 0 & 0 \\
44 & 1.52894765E-03 & 1.52894761E-03 & 2.07246E-06 & 92 & 0 & 0 & 0 \\
45 & 2.25918639E-03 & 2.25918647E-03 & 3.61502E-06 & 93 & 0 & 0 & 0 \\
46 & 2.22762401E-03 & 2.22762395E-03 & 2.75076E-06 & 94 & 0 & 0 & 0 \\
47 & 1.48307979E-03 & 1.48307974E-03 & 3.20316E-06 & 95 & 0 & 0 & 0 \\
48 & 0 & 0 & 0 & 96 & 0 & 0 & 0 \\
\end{bmatrix}

{{< /details >}}



## 3.2 Gauss Integration 2x2

{{< details "**Results from Gauss Integration 2x2:** - (click to expand)" close >}}

**Maximum error devation**: 3,99501E-06 %

![Katex](\begin{bmatrix}
\text{Node} & \text{Gauss 2x2 Matlab (m)} & \text{Siemens NX (m)} & \text{Relative Error (%)} & \text{Node} & \text{Gauss 2x2 (m)} & \text{Siemens NX (m)} & \text{Relative Error (%)} \\
1 & 0 & 0 & 0 & 49 & 0 & 0 & 0 \\
2 & 0 & 0 & 0 & 50 & 1.52894765E-03 & 1.52894761E-03 & 2.07246E-06 \\
3 & 0 & 0 & 0 & 51 & 2.25918639E-03 & 2.25918647E-03 & 3.61502E-06 \\
4 & 0 & 0 & 0 & 52 & 2.22762401E-03 & 2.22762395E-03 & 2.75076E-06 \\
5 & 0 & 0 & 0 & 53 & 1.48307979E-03 & 1.48307974E-03 & 3.20316E-06 \\
6 & 0 & 0 & 0 & 54 & 0 & 0 & 0 \\
7 & 0 & 0 & 0 & 55 & 0 & 0 & 0 \\
8 & 0 & 0 & 0 & 56 & 0 & 0 & 0 \\
9 & 0 & 0 & 0 & 57 & 0 & 0 & 0 \\
10 & 0 & 0 & 0 & 58 & 0 & 0 & 0 \\
11 & 0 & 0 & 0 & 59 & 0 & 0 & 0 \\
12 & 0 & 0 & 0 & 60 & 0 & 0 & 0 \\
13 & 0 & 0 & 0 & 61 & 0 & 0 & 0 \\
14 & 0 & 0 & 0 & 62 & 0 & 0 & 0 \\
15 & 0 & 0 & 0 & 63 & 0 & 0 & 0 \\
16 & 7.19690280E-04 & 7.19690288E-04 & 1.08731E-06 & 64 & 1.60173557E-03 & 1.60173560E-03 & 1.30914E-06 \\
17 & 8.63924007E-04 & 8.63923982E-04 & 2.91831E-06 & 65 & 2.18252517E-03 & 2.18252512E-03 & 2.49479E-06 \\
18 & 8.93021588E-04 & 8.93021584E-04 & 4.38814E-07 & 66 & 2.11690066E-03 & 2.11690064E-03 & 1.01038E-06 \\
19 & 8.99847643E-04 & 8.99847655E-04 & 1.34474E-06 & 67 & 1.40940987E-03 & 1.40940992E-03 & 3.31030E-06 \\
20 & 9.05049620E-04 & 9.05049616E-04 & 5.18946E-07 & 68 & 0 & 0 & 0 \\
21 & 9.22107228E-04 & 9.22107254E-04 & 2.87960E-06 & 69 & 0 & 0 & 0 \\
22 & 9.91068761E-04 & 9.91068780E-04 & 1.99588E-06 & 70 & 7.19690280E-04 & 7.19690288E-04 & 1.08731E-06 \\
23 & 1.27384391E-03 & 1.27384393E-03 & 1.55619E-06 & 71 & 8.63924007E-04 & 8.63923982E-04 & 2.91831E-06 \\
24 & 1.44339051E-03 & 1.44339050E-03 & 8.95264E-07 & 72 & 8.93021588E-04 & 8.93021584E-04 & 4.38814E-07 \\
25 & 1.64555815E-03 & 1.64555816E-03 & 1.13774E-06 & 73 & 8.99847643E-04 & 8.99847655E-04 & 1.34474E-06 \\
26 & 1.55521043E-03 & 1.55521045E-03 & 8.87392E-07 & 74 & 9.05049620E-04 & 9.05049616E-04 & 5.18946E-07 \\
27 & 1.07160366E-03 & 1.07160362E-03 & 3.99501E-06 & 75 & 9.22107228E-04 & 9.22107254E-04 & 2.87960E-06 \\
28 & 0 & 0 & 0 & 76 & 9.91068761E-04 & 9.91068780E-04 & 1.99588E-06 \\
29 & 0 & 0 & 0 & 77 & 1.27384391E-03 & 1.27384393E-03 & 1.55619E-06 \\
30 & 0 & 0 & 0 & 78 & 1.44339051E-03 & 1.44339050E-03 & 8.95264E-07 \\
31 & 0 & 0 & 0 & 79 & 1.64555815E-03 & 1.64555816E-03 & 1.13774E-06 \\
32 & 0 & 0 & 0 & 80 & 1.55521043E-03 & 1.55521045E-03 & 8.87392E-07 \\
33 & 0 & 0 & 0 & 81 & 1.07160366E-03 & 1.07160362E-03 & 3.99501E-06 \\
34 & 0 & 0 & 0 & 82 & 0 & 0 & 0 \\
35 & 0 & 0 & 0 & 83 & 0 & 0 & 0 \\
36 & 0 & 0 & 0 & 84 & 0 & 0 & 0 \\
37 & 0 & 0 & 0 & 85 & 0 & 0 & 0 \\
38 & 1.60173557E-03 & 1.60173560E-03 & 1.30914E-06 & 86 & 0 & 0 & 0 \\
39 & 2.18252517E-03 & 2.18252512E-03 & 2.49479E-06 & 87 & 0 & 0 & 0 \\
40 & 2.11690066E-03 & 2.11690064E-03 & 1.01038E-06 & 88 & 0 & 0 & 0 \\
41 & 1.40940987E-03 & 1.40940992E-03 & 3.31030E-06 & 89 & 0 & 0 & 0 \\
42 & 0 & 0 & 0 & 90 & 0 & 0 & 0 \\
43 & 0 & 0 & 0 & 91 & 0 & 0 & 0 \\
44 & 1.52894765E-03 & 1.52894761E-03 & 2.07246E-06 & 92 & 0 & 0 & 0 \\
45 & 2.25918639E-03 & 2.25918647E-03 & 3.61502E-06 & 93 & 0 & 0 & 0 \\
46 & 2.22762401E-03 & 2.22762395E-03 & 2.75076E-06 & 94 & 0 & 0 & 0 \\
47 & 1.48307979E-03 & 1.48307974E-03 & 3.20316E-06 & 95 & 0 & 0 & 0 \\
48 & 0 & 0 & 0 & 96 & 0 & 0 & 0 \\
\end{bmatrix})


{{< /details >}}

## 3.3 Gauss Integration 2x1

{{< details "**Results from Gauss Integration 2x1:** - (click to expand)" close >}}

**Maximum error devation**: 8,605963869 %


\begin{bmatrix}
\text{Node} & \text{Gauss 2x1 Matlab (m)} & \text{Siemens NX (m)} & \text{Relative Error (%)} & \text{Node} & \text{Gauss 2x1 (m)} & \text{Siemens NX (m)} & \text{Relative Error (%)} \\
1 & 0 & 0 & 0 & 49 & 0 & 0 & 0 \\
2 & 0 & 0 & 0 & 50 & 1.55467375E-03 & 1.52894761E-03 & 1.68260E+00 \\
3 & 0 & 0 & 0 & 51 & 2.31711275E-03 & 2.25918647E-03 & 2.56403E+00 \\
4 & 0 & 0 & 0 & 52 & 2.27010618E-03 & 2.22762395E-03 & 1.90706E+00 \\
5 & 0 & 0 & 0 & 53 & 1.50677021E-03 & 1.48307974E-03 & 1.59738E+00 \\
6 & 0 & 0 & 0 & 54 & 0 & 0 & 0 \\
7 & 0 & 0 & 0 & 55 & 0 & 0 & 0 \\
8 & 0 & 0 & 0 & 56 & 0 & 0 & 0 \\
9 & 0 & 0 & 0 & 57 & 0 & 0 & 0 \\
10 & 0 & 0 & 0 & 58 & 0 & 0 & 0 \\
11 & 0 & 0 & 0 & 59 & 0 & 0 & 0 \\
12 & 0 & 0 & 0 & 60 & 0 & 0 & 0 \\
13 & 0 & 0 & 0 & 61 & 0 & 0 & 0 \\
14 & 0 & 0 & 0 & 62 & 0 & 0 & 0 \\
15 & 0 & 0 & 0 & 63 & 0 & 0 & 0 \\
16 & 7.71981892E-04 & 7.19690288E-04 & 7.26585E+00 & 64 & 1.71631749E-03 & 1.60173560E-03 & 7.15361E+00 \\
17 & 8.81801159E-04 & 8.63923982E-04 & 2.06930E+00 & 65 & 2.23381865E-03 & 2.18252512E-03 & 2.35019E+00 \\
18 & 8.97488491E-04 & 8.93021584E-04 & 5.00201E-01 & 66 & 2.16444946E-03 & 2.11690064E-03 & 2.24615E+00 \\
19 & 9.00185258E-04 & 8.99847655E-04 & 3.75178E-02 & 67 & 1.43151753E-03 & 1.40940992E-03 & 1.56857E+00 \\
20 & 9.02974740E-04 & 9.05049616E-04 & 2.29255E-01 & 68 & 0 & 0 & 0 \\
21 & 9.16473288E-04 & 9.22107254E-04 & 6.10988E-01 & 69 & 0 & 0 & 0 \\
22 & 9.89275672E-04 & 9.91068780E-04 & 1.80927E-01 & 70 & 7.71981892E-04 & 7.19690288E-04 & 7.26585E+00 \\
23 & 1.38347047E-03 & 1.27384393E-03 & 8.60596E+00 & 71 & 8.81801159E-04 & 8.63923982E-04 & 2.06930E+00 \\
24 & 1.41159469E-03 & 1.44339050E-03 & 2.20286E+00 & 72 & 8.97488491E-04 & 8.93021584E-04 & 5.00201E-01 \\
25 & 1.66836160E-03 & 1.64555816E-03 & 1.38576E+00 & 73 & 9.00185258E-04 & 8.99847655E-04 & 3.75178E-02 \\
26 & 1.58512536E-03 & 1.55521045E-03 & 1.92353E+00 & 74 & 9.02974740E-04 & 9.05049616E-04 & 2.29255E-01 \\
27 & 1.10986691E-03 & 1.07160362E-03 & 3.57066E+00 & 75 & 9.16473288E-04 & 9.22107254E-04 & 6.10988E-01 \\
28 & 0 & 0 & 0 & 76 & 9.89275672E-04 & 9.91068780E-04 & 1.80927E-01 \\
29 & 0 & 0 & 0 & 77 & 1.38347047E-03 & 1.27384393E-03 & 8.60596E+00 \\
30 & 0 & 0 & 0 & 78 & 1.41159469E-03 & 1.44339050E-03 & 2.20286E+00 \\
31 & 0 & 0 & 0 & 79 & 1.66836160E-03 & 1.64555816E-03 & 1.38576E+00 \\
32 & 0 & 0 & 0 & 80 & 1.58512536E-03 & 1.55521045E-03 & 1.92353E+00 \\
33 & 0 & 0 & 0 & 81 & 1.10986691E-03 & 1.07160362E-03 & 3.57066E+00 \\
34 & 0 & 0 & 0 & 82 & 0 & 0 & 0 \\
35 & 0 & 0 & 0 & 83 & 0 & 0 & 0 \\
36 & 0 & 0 & 0 & 84 & 0 & 0 & 0 \\
37 & 0 & 0 & 0 & 85 & 0 & 0 & 0 \\
38 & 1.71631749E-03 & 1.60173560E-03 & 7.15361E+00 & 86 & 0 & 0 & 0 \\
39 & 2.23381865E-03 & 2.18252512E-03 & 2.35019E+00 & 87 & 0 & 0 & 0 \\
40 & 2.16444946E-03 & 2.11690064E-03 & 2.24615E+00 & 88 & 0 & 0 & 0 \\
41 & 1.43151753E-03 & 1.40940992E-03 & 1.56857E+00 & 89 & 0 & 0 & 0 \\
42 & 0 & 0 & 0 & 90 & 0 & 0 & 0 \\
43 & 0 & 0 & 0 & 91 & 0 & 0 & 0 \\
44 & 1.55467375E-03 & 1.52894761E-03 & 1.68260E+00 & 92 & 0 & 0 & 0 \\
45 & 2.31711275E-03 & 2.25918647E-03 & 2.56403E+00 & 93 & 0 & 0 & 0 \\
46 & 2.27010618E-03 & 2.22762395E-03 & 1.90706E+00 & 94 & 0 & 0 & 0 \\
47 & 1.50677021E-03 & 1.48307974E-03 & 1.59738E+00 & 95 & 0 & 0 & 0 \\
48 & 0 & 0 & 0 & 96 & 0 & 0 & 0 \\
\end{bmatrix}

{{< /details >}}


## 3.4 Gauss Integration 1x2

{{< details "**Results from Gauss Integration 1x2:** - (click to expand)" close >}}

**Maximum error devation**: 4,839239833 %

\begin{bmatrix}
\text{Node} & \text{Gauss 1x2 Matlab (m)} & \text{Siemens NX (m)} & \text{Relative Error (%)} & \text{Node} & \text{Gauss 1x2 (m)} & \text{Siemens NX (m)} & \text{Relative Error (%)} \\
1 & 0 & 0 & 0 & 49 & 0 & 0 & 0 \\
2 & 0 & 0 & 0 & 50 & 1.53554270E-03 & 1.52894761E-03 & 4.31348E-01 \\
3 & 0 & 0 & 0 & 51 & 2.28579069E-03 & 2.25918647E-03 & 1.17760E+00 \\
4 & 0 & 0 & 0 & 52 & 2.24819251E-03 & 2.22762395E-03 & 9.23341E-01 \\
5 & 0 & 0 & 0 & 53 & 1.49451539E-03 & 1.48307974E-03 & 7.71074E-01 \\
6 & 0 & 0 & 0 & 54 & 0 & 0 & 0 \\
7 & 0 & 0 & 0 & 55 & 0 & 0 & 0 \\
8 & 0 & 0 & 0 & 56 & 0 & 0 & 0 \\
9 & 0 & 0 & 0 & 57 & 0 & 0 & 0 \\
10 & 0 & 0 & 0 & 58 & 0 & 0 & 0 \\
11 & 0 & 0 & 0 & 59 & 0 & 0 & 0 \\
12 & 0 & 0 & 0 & 60 & 0 & 0 & 0 \\
13 & 0 & 0 & 0 & 61 & 0 & 0 & 0 \\
14 & 0 & 0 & 0 & 62 & 0 & 0 & 0 \\
15 & 0 & 0 & 0 & 63 & 0 & 0 & 0 \\
16 & 7.54517827E-04 & 7.19690288E-04 & 4.83924E+00 & 64 & 1.65082354E-03 & 1.60173560E-03 & 3.06467E+00 \\
17 & 8.76504467E-04 & 8.63923982E-04 & 1.45620E+00 & 65 & 2.20438567E-03 & 2.18252512E-03 & 1.00162E+00 \\
18 & 8.96336618E-04 & 8.93021584E-04 & 3.71215E-01 & 66 & 2.13630448E-03 & 2.11690064E-03 & 9.16616E-01 \\
19 & 9.00241019E-04 & 8.99847655E-04 & 4.37145E-02 & 67 & 1.41926507E-03 & 1.40940992E-03 & 6.99239E-01 \\
20 & 9.03896089E-04 & 9.05049616E-04 & 1.27454E-01 & 68 & 0 & 0 & 0 \\
21 & 9.18923528E-04 & 9.22107254E-04 & 3.45266E-01 & 69 & 0 & 0 & 0 \\
22 & 9.89187212E-04 & 9.91068780E-04 & 1.89852E-01 & 70 & 7.54517827E-04 & 7.19690288E-04 & 4.83924E+00 \\
23 & 1.31978114E-03 & 1.27384393E-03 & 3.60619E+00 & 71 & 8.76504467E-04 & 8.63923982E-04 & 1.45620E+00 \\
24 & 1.43488415E-03 & 1.44339050E-03 & 5.89331E-01 & 72 & 8.96336618E-04 & 8.93021584E-04 & 3.71215E-01 \\
25 & 1.65472031E-03 & 1.64555816E-03 & 5.56781E-01 & 73 & 9.00241019E-04 & 8.99847655E-04 & 4.37145E-02 \\
26 & 1.56813851E-03 & 1.55521045E-03 & 8.31274E-01 & 74 & 9.03896089E-04 & 9.05049616E-04 & 1.27454E-01 \\
27 & 1.08712814E-03 & 1.07160362E-03 & 1.44872E+00 & 75 & 9.18923528E-04 & 9.22107254E-04 & 3.45266E-01 \\
28 & 0 & 0 & 0 & 76 & 9.89187212E-04 & 9.91068780E-04 & 1.89852E-01 \\
29 & 0 & 0 & 0 & 77 & 1.31978114E-03 & 1.27384393E-03 & 3.60619E+00 \\
30 & 0 & 0 & 0 & 78 & 1.43488415E-03 & 1.44339050E-03 & 5.89331E-01 \\
31 & 0 & 0 & 0 & 79 & 1.65472031E-03 & 1.64555816E-03 & 5.56781E-01 \\
32 & 0 & 0 & 0 & 80 & 1.56813851E-03 & 1.55521045E-03 & 8.31274E-01 \\
33 & 0 & 0 & 0 & 81 & 1.08712814E-03 & 1.07160362E-03 & 1.44872E+00 \\
34 & 0 & 0 & 0 & 82 & 0 & 0 & 0 \\
35 & 0 & 0 & 0 & 83 & 0 & 0 & 0 \\
36 & 0 & 0 & 0 & 84 & 0 & 0 & 0 \\
37 & 0 & 0 & 0 & 85 & 0 & 0 & 0 \\
38 & 1.65082354E-03 & 1.60173560E-03 & 3.06467E+00 & 86 & 0 & 0 & 0 \\
39 & 2.20438567E-03 & 2.18252512E-03 & 1.00162E+00 & 87 & 0 & 0 & 0 \\
40 & 2.13630448E-03 & 2.11690064E-03 & 9.16616E-01 & 88 & 0 & 0 & 0 \\
41 & 1.41926507E-03 & 1.40940992E-03 & 6.99239E-01 & 89 & 0 & 0 & 0 \\
42 & 0 & 0 & 0 & 90 & 0 & 0 & 0 \\
43 & 0 & 0 & 0 & 91 & 0 & 0 & 0 \\
44 & 1.53554270E-03 & 1.52894761E-03 & 4.31348E-01 & 92 & 0 & 0 & 0 \\
45 & 2.28579069E-03 & 2.25918647E-03 & 1.17760E+00 & 93 & 0 & 0 & 0 \\
46 & 2.24819251E-03 & 2.22762395E-03 & 9.23341E-01 & 94 & 0 & 0 & 0 \\
47 & 1.49451539E-03 & 1.48307974E-03 & 7.71074E-01 & 95 & 0 & 0 & 0 \\
48 & 0 & 0 & 0 & 96 & 0 & 0 & 0 \\
\end{bmatrix}
{{< /details >}}


## 3.5 Gauss Integration 1x1

{{< details "**Results from Gauss Integration 1x1:** - (click to expand)" close >}}

**Maximum error devation**: 15,19098164 %

\begin{bmatrix}
[katex](\text{Node} & \text{Gauss 1x1 Matlab (m)} & \text{Siemens NX (m)} & \text{Relative Error (%)} & \text{Node} & \text{Gauss 1x1 (m)} & \text{Siemens NX (m)} & \text{Relative Error (%)} \\
1 & 0 & 0 & 0 & 49 & 0 & 0 & 0 \\
2 & 0 & 0 & 0 & 50 & 1.54947421E-03 & 1.52894761E-03 & 1.34253E+00 \\
3 & 0 & 0 & 0 & 51 & 2.36381783E-03 & 2.25918647E-03 & 4.63137E+00 \\
4 & 0 & 0 & 0 & 52 & 2.28905316E-03 & 2.22762395E-03 & 2.75761E+00 \\
5 & 0 & 0 & 0 & 53 & 1.52154662E-03 & 1.48307974E-03 & 2.59372E+00 \\
6 & 0 & 0 & 0 & 54 & 0 & 0 & 0 \\
7 & 0 & 0 & 0 & 55 & 0 & 0 & 0 \\
8 & 0 & 0 & 0 & 56 & 0 & 0 & 0 \\
9 & 0 & 0 & 0 & 57 & 0 & 0 & 0 \\
10 & 0 & 0 & 0 & 58 & 0 & 0 & 0 \\
11 & 0 & 0 & 0 & 59 & 0 & 0 & 0 \\
12 & 0 & 0 & 0 & 60 & 0 & 0 & 0 \\
13 & 0 & 0 & 0 & 61 & 0 & 0 & 0 \\
14 & 0 & 0 & 0 & 62 & 0 & 0 & 0 \\
15 & 0 & 0 & 0 & 63 & 0 & 0 & 0 \\
16 & 8.18181994E-04 & 7.19690288E-04 & 1.36853E+01 & 64 & 1.81961063E-03 & 1.60173560E-03 & 1.36024E+01 \\
17 & 8.92563936E-04 & 8.63923982E-04 & 3.31510E+00 & 65 & 2.23407901E-03 & 2.18252512E-03 & 2.36212E+00 \\
18 & 8.99345298E-04 & 8.93021584E-04 & 7.08126E-01 & 66 & 2.19713330E-03 & 2.11690064E-03 & 3.79010E+00 \\
19 & 9.00174828E-04 & 8.99847655E-04 & 3.63587E-02 & 67 & 1.43676921E-03 & 1.40940992E-03 & 1.94119E+00 \\
20 & 9.01645310E-04 & 9.05049616E-04 & 3.76146E-01 & 68 & 0 & 0 & 0 \\
21 & 9.11577385E-04 & 9.22107254E-04 & 1.14194E+00 & 69 & 0 & 0 & 0 \\
22 & 9.81050299E-04 & 9.91068780E-04 & 1.01088E+00 & 70 & 8.18181994E-04 & 7.19690288E-04 & 1.36853E+01 \\
23 & 1.46735332E-03 & 1.27384393E-03 & 1.51910E+01 & 71 & 8.92563936E-04 & 8.63923982E-04 & 3.31510E+00 \\
24 & 1.37127291E-03 & 1.44339050E-03 & 4.99640E+00 & 72 & 8.99345298E-04 & 8.93021584E-04 & 7.08126E-01 \\
25 & 1.69857374E-03 & 1.64555816E-03 & 3.22174E+00 & 73 & 9.00174828E-04 & 8.99847655E-04 & 3.63587E-02 \\
26 & 1.59362244E-03 & 1.55521045E-03 & 2.46989E+00 & 74 & 9.01645310E-04 & 9.05049616E-04 & 3.76146E-01 \\
27 & 1.13329356E-03 & 1.07160362E-03 & 5.75679E+00 & 75 & 9.11577385E-04 & 9.22107254E-04 & 1.14194E+00 \\
28 & 0 & 0 & 0 & 76 & 9.81050299E-04 & 9.91068780E-04 & 1.01088E+00 \\
29 & 0 & 0 & 0 & 77 & 1.46735332E-03 & 1.27384393E-03 & 1.51910E+01 \\
30 & 0 & 0 & 0 & 78 & 1.37127291E-03 & 1.44339050E-03 & 4.99640E+00 \\
31 & 0 & 0 & 0 & 79 & 1.69857374E-03 & 1.64555816E-03 & 3.22174E+00 \\
32 & 0 & 0 & 0 & 80 & 1.59362244E-03 & 1.55521045E-03 & 2.46989E+00 \\
33 & 0 & 0 & 0 & 81 & 1.13329356E-03 & 1.07160362E-03 & 5.75679E+00 \\
34 & 0 & 0 & 0 & 82 & 0 & 0 & 0 \\
35 & 0 & 0 & 0 & 83 & 0 & 0 & 0 \\
36 & 0 & 0 & 0 & 84 & 0 & 0 & 0 \\
37 & 0 & 0 & 0 & 85 & 0 & 0 & 0 \\
38 & 1.81961063E-03 & 1.60173560E-03 & 1.36024E+01 & 86 & 0 & 0 & 0 \\
39 & 2.23407901E-03 & 2.18252512E-03 & 2.36212E+00 & 87 & 0 & 0 & 0 \\
40 & 2.19713330E-03 & 2.11690064E-03 & 3.79010E+00 & 88 & 0 & 0 & 0 \\
41 & 1.43676921E-03 & 1.40940992E-03 & 1.94119E+00 & 89 & 0 & 0 & 0 \\
42 & 0 & 0 & 0 & 90 & 0 & 0 & 0 \\
43 & 0 & 0 & 0 & 91 & 0 & 0 & 0 \\
44 & 1.54947421E-03 & 1.52894761E-03 & 1.34253E+00 & 92 & 0 & 0 & 0 \\
45 & 2.36381783E-03 & 2.25918647E-03 & 4.63137E+00 & 93 & 0 & 0 & 0 \\
46 & 2.28905316E-03 & 2.22762395E-03 & 2.75761E+00 & 94 & 0 & 0 & 0 \\
47 & 1.52154662E-03 & 1.48307974E-03 & 2.59372E+00 & 95 & 0 & 0 & 0 \\
48 & 0 & 0 & 0 & 96 & 0 & 0 & 0 \\
\end{bmatrix})
{{< /details >}}


## 3.6 Calculation of Torsion Coefficients NX vs. Matlab

The nodal solution values, node positions, and connectivity matrix data in NX were used to compute the torsion constant, J, akin to the calculations performed for J in the analytical or Gauss integrations in Matlab.

Calculating the value of J for various refinements yielded:

{{< katex display >}}
\begin{bmatrix}
    \text{Number of nodes} & J \text{computed from NX} (m^4) \\
    96 & 6.11911850 \times 10^{-5} \\
    234 & 6.85679916 \times 10^{-5} \\
    600 & 7.06099102 \times 10^{-5} \\
    1765 & 7.13958341 \times 10^{-5} \\
    4720 & 7.16633419 \times 10^{-5} \\
    41461 & 7.18205027 \times 10^{-5} \\
\end{bmatrix}
{{< /katex >}}

For the considered 96 nodes:

{{< katex display >}}
\begin{bmatrix}
    \text{J NX (m^4)} & \text{J Matlab (m^4)} & \text{Relative Error (%)} \\
    6.1191185 \times 10^{-5} & 6.11890982 \times 10^{-5} & 3.41041 \times 10^{-3} \\
\end{bmatrix}
{{< /katex >}}

The conclusion drawn from these findings is that as the mesh undergoes refinement, the constant torsion value J tends to stabilize. It's evident from these results that the approximation done by Matlab remains quite acceptable. This is highlighted by the fact that the J value derived from Matlab aligns closely with the analytical integration value and is also in close proximity to the J value computed in NX for 96 nodes, which itself approximates the converged value.

