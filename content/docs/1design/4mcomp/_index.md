---
weight: 4
bookHidden: false
title: "FEM Formulation"
---

# **FEM: A Virtual Tetris**

## 1 Mathematical Background

{{< hint tip >}}
If you wish to skip the theoretical and implementation details, and would like to know my thoughts on attempting to code a finite element method software or alternatively develop a SDK for existing SW, [please click here to jump to the summarized findings Ch. 4 - My Considerations](https://ricardochin.com/docs/1design/4mcomp/#4-my-considerations).{{< /hint >}}

### 1.1. Partial Differential Equations (PDEs)

In engineering and physics, problems are often described by partial differential equations (PDEs). The "strong form" refers to the original differential equation that must hold exactly throughout the domain, including both the governing equation and the boundary conditions.

However, there are cases where directly solving the strong form might be challenging due to complexities in the equation, irregular geometries, or varying boundary conditions. This is where the "weak formulation" or ("weak form") becomes advantageous.

The weak form introduces a relaxation of the constraints imposed by the strong form by multiplying the governing equation with a weight function (typically a test function) and integrating it over the domain. This relaxation allows for more flexibility in solution techniques and offers several advantages:

- Handling Boundary Conditions: The weak form often simplifies the imposition of different types of boundary conditions.

- Solvability: Some differential equations in the strong form might not have exact solutions or might be difficult to solve directly. The weak form can lead to a more manageable equation for numerical methods.

- Adaptability to Numerical Methods: Finite element, boundary element, and other numerical methods are often more easily applied to weak formulations due to the integral nature of the equations.

- Handling Discontinuities: In problems involving discontinuities or singularities, the weak form can handle these cases more effectively.

- Minimization of Requirements: In some cases, the requirement for differentiability or the number of times an equation needs to be differentiated is reduced, easing computational complexity.

Overall, the weak form is favoured in many situations because it relaxes the strictness of the original problem while maintaining the essential properties required for solutions, making it more adaptable for various solution techniques, especially in numerical analysis.

### 1.2. Strong Form

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

### 1.3. Weak Form

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

### 1.4. Analytical Integration

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

### 1.5. Numerical Integration

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

### 1.6. Torsional Constant 

After calculating the nodal solution, the torsion constant J can be determined by:

{{< katex display >}}
J^e = \int_0^b \int_0^a 2 \psi^e dxdy = \frac{ab}{2}(\psi_1 + \psi_2 + \psi_3 + \psi_4) \qquad J = \sum_i J_i^e
{{< /katex >}}  

### 1.7. Shear Stresses

{{< katex display >}}
\tau_{yz} =  -G \theta \frac{\partial \psi}{\partial x} = -G \theta (\frac{\partial \phi_1}{\partial \overline{x}}\psi_1 + \frac{\partial \phi_2}{\partial \overline{x}}\psi_2 + \frac{\partial \phi_3}{\partial \overline{x}}\psi_3 + \frac{\partial \phi_4}{\partial \overline{x}}\psi_4)
{{< /katex >}}  

{{< katex display >}}
\tau_{xz} =  G \theta \frac{\partial \psi}{\partial y} = G \theta (\frac{\partial \phi_1}{\partial \overline{y}}\psi_1 + \frac{\partial \phi_2}{\partial \overline{y}}\psi_2 + \frac{\partial \phi_3}{\partial \overline{y}}\psi_3 + \frac{\partial \phi_4}{\partial \overline{y}}\psi_4)
{{< /katex >}} 

{{< katex display >}}
\tau_{xy} = \sqrt{\tau_{yz}^2 +\tau_{xz}^2}
{{< /katex >}} 

## 2 Time for some coding!

### 2.1. READ_ME

The program starts by executing the script ["MainG10.m"](https://github.com/roaked/fem-math-formulation/tree/main/code) **directly available on my github page** and should contain all the subfunctions and their respective 'txt' files in the respective folder. Once the program is initiated, the user inputs the data file name in the form of "nome.txt." The data file should contain the node coordinates, connectivity matrix, and boundary conditions. The data file created for this problem is named ["dadosg10.txt"](https://github.com/roaked/fem-math-formulation/tree/main/code).

After submitting the data file, the program reads the file and automatically opens a figure displaying the mesh with numbered nodes and elements. Simultaneously, a menu with various options is displayed. The user should first choose the type of integration and then select the desired plots for shear stresses, stress contour lines, and nodal solutions. Submitting the options is done by entering the corresponding number. The program is terminated by entering the number 0.

Upon program completion, a file is created containing the data obtained from the last integration performed.

![sds](https://live.staticflickr.com/65535/53347766202_f8a2c9e042_q.jpg) 



**REMARK:** The node numbering associated with a given element in the incidence matrix is done counterclockwise, as shown in the accompanying image. Any other numbering will yield incorrect results for solving this problem. The coordinates in the node matrix are in SI units, therefore in meters. Introducing values in different units in the data file will result in incorrect units in the final program output. The material conductivity or property should be 1; if it's not, the results won't be valid for this torsion problem. Regarding boundary conditions, only essential boundary conditions equal to zero are allowed. However, they will be set to zero if another finite value is associated. The distributed loading assumes the value 2; however, if the value differs, the program will still provide results—though they may not be reasonable for solving the initially presented problem. All other boundary conditions should be null, and if they're not, an error message will be displayed.



### 2.2. Validation Based on Book

The developed program has been tested for a known problem described in the book ["Introduction to the Finite Element Method (3rd edition)" by J.N. Reddy](https://books.google.at/books/about/An_Introduction_to_the_Finite_Element_Me.html?id=8gqnRwAACAAJ&redir_esc=y) - **Example 8.5.6**. In this example, torsion of a square section bar was considered. As the problem is axisymmetric, nodal solution and shear stresses were calculated for a quarter of the square. The following results were obtained at the nodes for the nodal solution, using a 4x4 mesh:

{{< katex display >}}
\begin{bmatrix}
x & y & \text{Analytical} & \text{Gauss 2x2} & \text{Gauss 1x2} & \text{Gauss 1x1} & \text{Reddy} \\
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
x & y & \text{Analytical} & \text{Gauss 2x2} & \text{Gauss 1x2} & \text{Gauss 1x1} & \text{Known Sol.} \\
0.06250 & 0.0625 & 0.0618 & 0.0618 & 0.0621 & 0.0625 & 0.0618 \\
0.1875 & 0.0625 & 0.1942 & 0.1942 & 0.1955 & 0.1970 & 0.1939 \\
0.3125 & 0.0625 & 0.3529 & 0.3529 & 0.3553 & 0.3577 & 0.3516 \\
0.4375 & 0.0625 & 0.5528 & 0.5528 & 0.5560 & 0.5593 & 0.5504 \\
\end{bmatrix}
{{< /katex >}}

It's observed that stiffness matrices obtained through analytical integration or Gauss 2x2 are closely aligned with the known values. In fact, the values from analytical integration and Gauss 2x2 are identical when approximated to 4 decimal places. The fewer points used in the Gauss integration, the greater the error, with Gauss 1x1 showing the largest discrepancy between obtained and known values. The largest relative error found in analytical integration and Gauss 2x2 was 0.436%. For Gauss 1x2 integration, the largest relative error was 1.05%, while Gauss 1x1 integration showed the largest relative error of 1.73%. Overall, the values are close to the known ones, indicating that the developed program is functioning correctly.

## 3 Comparison with FEM commercial software Siemens NX

In addition, the problem was tackled by resorting to the commercial software NX, which unfortunately lacks a dedicated solver for torsion problems. As a workaround, the issue was addressed using the *Sol 153 solver – Steady State Nonlinear Heat Transfer*. This approach was selected due to the analogy between torsion and heat problems. Specific conditions were applied, including setting a boundary temperature at T = 0°C, introducing a uniform heat generation of 2 W/m³ throughout the section, and assigning a thermal conductivity value of K = 1 to the material. To compare results, the Prandtl function was solved using different integration methods in Matlab against the outcomes obtained through NX.


The data from the upcoming tables indicates that the minimum error occurs in the analytical integration type and in the numerical integration type with 2x2 Gauss points. As observed in the known problem, the error values are identical for these two integration types. It's worth noting that in the 1x1 Gauss points integration, there is a considerable relative error percentage (15.2%), which decreases in the case of 2x1 (8.6%) and 1x2 Gauss points (4.84%)

### 3.1. Analytical Integration

**Maximum error deviation**: 3,99501E-06%




### 3.2. Gauss Integration 2x2



**Maximum error deviation**: 3,99501E-06 %


### 3.3. Gauss Integration 2x1


**Maximum error deviation**: 8,605963869 %


### 3.4. Gauss Integration 1x2



**Maximum error deviation**: 4,839239833 %



### 3.5. Gauss Integration 1x1



**Maximum error deviation**: 15,19098164 %



### 3.6. Torsion Coefficients NX vs. Matlab

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
\text{J NX} (m^4) & \text{J Matlab} (m^4) & \text{Relative Error}  (\%) \\
6.1191185 \times 10^{-5} & 6.11890982 \times 10^{-5} & 3.41041 \times 10^{-3} \\
\end{bmatrix}
{{< /katex >}}

The conclusion drawn from these findings is that as the mesh undergoes refinement, the constant torsion value J tends to stabilize. It's evident from these results that the approximation done by Matlab remains quite acceptable. This is highlighted by the fact that the J value derived from Matlab aligns closely with the analytical integration value and is also in close proximity to the J value computed in NX for 96 nodes, which itself approximates the converged value.

### 3.7. Max and Min Stresses NX vs. Matlab

{{< katex display >}}
\begin{bmatrix}
\text{Stresses} & \text{Matlab} & \text{NX} & \text{Relative Error} (\%) \\
\text{Maximum (Pa)} & 1279.447 & 1279.404 & 3.40677E-03 \\
\text{Minimum (Pa)} & 25.790852 & 25.789972 & 3.41135E-03 \\
\end{bmatrix}
{{< /katex >}}

The comparison between the results from Matlab (analytical integration) and NX led to the conclusion that quite satisfactory outcomes were attained. It was observed that the relative error obtained for the extreme values falls within the order of {{< katex >}}10^{-3}{{< /katex >}} in percentage.

### 3.8. Results Siemens NX

The initial mesh comprises 96 nodes. The mesh underwent 5 refinements to analyze the convergence of values. The results for the temperature distribution and gradient distribution are presented below for the six meshes, respectively on the left and right sides.

{{< details "**Meshing in NX Siemens:** Initial Mesh - (click to expand)" close >}}
![sddst](https://live.staticflickr.com/65535/53347732682_2ea88fae5e_n.jpg)
{{< /details >}}

Lastly, the 41468 nodes are represented for the final mesh refinement.

{{< details "**Meshing in NX Siemens:** Final Mesh - (click to expand)" close >}}
![sdasdaddst](https://live.staticflickr.com/65535/53349056300_d5a49424b2_z.jpg)
{{< /details >}}

### 3.9. Results MATLAB 

When the program is executed, it generates several figures such as the mesh representation with numbered elements and nodes, the contour lines representing stress, and the display of shear stresses with their respective distribution.

- **Elements and Nodes of the Mesh**

![sdasdsssaddst](https://live.staticflickr.com/65535/53349066455_9f29d491fa.jpg)

- **Shear Stress Ditribution**

![sdasdssdadassaddst](https://live.staticflickr.com/65535/53348939254_05f55130fc.jpg)

- **Shear Stress Contour Plot**

![sdasdsasdasdadassaddst](https://live.staticflickr.com/65535/53348616926_9b32186f03.jpg)

- **Nodal Solution: Analytical Integration**

![sdasdsasdasdsdadassaddst](https://live.staticflickr.com/65535/53347742997_a52836da0d_w.jpg)

- **Isometric View: Stress Isolines**

![sdasdsasdasdsdadassdssaddst](https://live.staticflickr.com/65535/53348939259_279f7d009e_w.jpg)


The nodal solution values, distribution of shear stresses, value of J, and torsional stiffness were, in addition, also obtained from the analytical integration, as reported. 

- Minimum stress of 1.28{{< katex >}}\times 10^{3}{{< /katex >}} Pa: (x,y) = (2.0{{< katex >}}\times 10^{-1}{{< /katex >}}, 1.1{{< katex >}}\times 10^{-1}{{< /katex >}}) m
- Minimum stress of 2.58{{< katex >}}\times 10^{1}{{< /katex >}} Pa: (x,y) = (2.4{{< katex >}}\times 10^{-1}{{< /katex >}}, 9.0{{< katex >}}\times 10^{-2}{{< /katex >}}) m    
- Torsion cofficient J: 6.11{{< katex >}}\times 10^{-5} Pa \cdot m^4{{< /katex >}}
- Torsional stiffness GJ: 4.65{{< katex >}}\times 10^{6} Pa \cdot m^4{{< /katex >}} 


To calculate GJ, the torsional stiffness, simply multiply the torsion coefficient J by the shear modulus, typically around 76 GPa.

## 4 My Considerations

While the current analysis has demonstrated promising alignment between the NX and Matlab outputs, future endeavours could focus on enhancing the model's precision and expanding its applicability. 

The reason for chosing MATLAB over other programming languages was mainly due to the fact that at this time I was most proficient with using MATLAB -- not being so confident in my skills in regards to other languages. Nonetheless, while developing the Finite Element Method (FEM) software in MATLAB, I encountered several limitations that made me reconsider the choice of programming language. MATLAB, with its user-friendly environment and extensive libraries, initially seemed suitable for rapid prototyping and algorithm testing. However, as the project progressed, certain factors became evident that might have been better addressed using alternatives such as: C++.

MATLAB's interpreted nature, though convenient for quick code iterations, posed performance concerns as the simulations scaled. The compiled nature of C++ would have offered better performance optimization, crucial for handling large datasets and computationally intensive FEM calculations. Control over memory management and low-level operations in C++ would have allowed for fine-tuning algorithms and achieving higher efficiency in handling complex simulations. 

Moreover, MATLAB's portability requirements often necessitated the distribution of MATLAB Runtime or Compiler for deployment on other systems (let alone requirements). In contrast, C++ offers inherent portability, allowing for easier cross-platform compatibility without additional runtime dependencies.

While MATLAB's user-friendliness is remarkable (honestly, most people should implement it and surely would rise up to the same outcome), after a brief web searching, I found that dedicated FEM libraries in C++, such as deal.II, DUNE, and libMesh, provide specialized tools for FEM simulations, offering robust solvers, mesh generation capabilities, and a community-driven framework specifically tailored for such applications. Are they really capable? I am not sure.

In retrospect, considering the computational demands, performance optimizations, and scalability required for the FEM software, implementing it in C++ might have offered a more efficient and scalable solution, despite MATLAB's initial ease of use, adequacy to simple cases and rapid development capabilities.  

However...

Instead of embarking on independent software development, I also explored alternative avenues. For instance, my experience with electron beam technology highlighted the crucial need for simulating phenomena within a vacuum chamber -- extremely low pressures. I discovered existing Software Development Kits tailored precisely for this purpose. Prominent software packages like ANSYS and COMSOL Multiphysics offer simulations that encompass this specific phenomenon.

This approach would have potentially offered a balanced solution, combining the advantages of a robust, commercially supported framework with the customization required for such project. However, it's essential to consider licensing costs and the level of customization achievable within the confines of these SDKs compared to developing from scratch in MATLAB or C++. As obviously, you are somehow restricted..


## 5 Future Work

As for attempting to improve my MATLAB model, I would probably steer in the following direction:

{{< hint example >}}

**Validation with Experimental Data**: Conducting experiments or obtaining real-world data to validate the computational findings could strengthen the model's reliability.

**Complex Geometries and Material Variations**: Expanding the study to encompass more complex geometries and diverse material properties can broaden the application scope of the torsion problem solution.

**Parametric Studies**: Conducting parametric studies to evaluate the impact of various parameters (such as material properties, dimensions, or loading conditions) on the torsional behavior can provide deeper insights.

**Integration of Advanced Techniques**: Exploring the integration of advanced numerical methods or simulation techniques could improve the computational efficiency and accuracy of the analysis.

{{< /hint >}}



