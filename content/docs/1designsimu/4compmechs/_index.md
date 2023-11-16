---
weight: 4
bookHidden: false
title: "FEM Formulation"

---

# 1. Formulating Finite Element Mathematical Models

## 1.1 Partial Differential Equations (PDEs)

In engineering and physics, problems are often described by partial differential equations (PDEs). The "strong form" refers to the original differential equation that must hold exactly throughout the domain, including both the governing equation and the boundary conditions.

However, there are cases where directly solving the strong form might be challenging due to complexities in the equation, irregular geometries, or varying boundary conditions. This is where the "weak formulation" or (weak form)becomes advantageous.

The weak form introduces a relaxation of the constraints imposed by the strong form by multiplying the governing equation with a weight function (typically a test function) and integrating it over the domain. This relaxation allows for more flexibility in solution techniques and offers several advantages:

- Handling Boundary Conditions: The weak form often simplifies the imposition of different types of boundary conditions.

- Solvability: Some differential equations in the strong form might not have exact solutions or might be difficult to solve directly. The weak form can lead to a more manageable equation for numerical methods.

- Adaptability to Numerical Methods: Finite element, boundary element, and other numerical methods are often more easily applied to weak formulations due to the integral nature of the equations.

- Handling Discontinuities: In problems involving discontinuities or singularities, the weak form can handle these cases more effectively.

- Minimization of Requirements: In some cases, the requirement for differentiability or the number of times an equation needs to be differentiated is reduced, easing computational complexity.

Overall, the weak form is favored in many situations because it relaxes the strictness of the original problem while maintaining the essential properties required for solutions, making it more adaptable for various solution techniques, especially in numerical analysis.

## 1.2 Strong Form

Let's begin by establishing the strong formulation of the problem, which serves as the foundation of our problem. In this formulation, we encapsulate the fundamental differential equation governing the system's behaviour. This equation, along with the precise boundary conditions and initial values, constitutes the direct representation of the underlying physical laws guiding the system's dynamics.

{{< katex display >}}
\theta - \nabla \cdot (k \nabla u) = f
{{< /katex >}}

For the torsional loading problem k = 1 and f = 2. The following equations indulges:

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

The weak formulation enables transforming the differential equation into an integral equation, where the initial approach involves integrating the equation across the entire domain. Rules are applied to obtain the weak formulation, namely, defining the residual by multiplying it with an arbitrary function, integrating by parts the highest-order term, and rearranging the equation.



