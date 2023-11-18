---
weight: 1
bookHidden: false
title: "Finite Element Model Formulation"

---

# 1. Mathematical Background

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

-

-
















