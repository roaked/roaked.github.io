---
weight: 1
bookHidden: false
title: "FEA Beam Instability Modes"
---

# **Beam Me Up, Scotty!**

## 1 Beams & Deformations

In this project, the process of modeling and simulating a beam with a thin-walled, constant cross-section in a C-profile along a length L will be described. [Siemens NX 12 software](https://plm.sw.siemens.com/en-US/nx/), a commercial finite element program, was used to obtain the global and local instability modes of the beam under compression loading. 

{{< hint info>}}
Consider the following values:

- Thickness t = 2.25L 
- Width B = 100 mm
- Height H = 189 mm
- Length L = 1445 mms 
- Young's Modulus E = 200 GPA
- Yield Stress σy = 700 MPa
- Poisson's Ratio = 0.3
{{< /hint>}}

In addition, boundary conditions (fixed support at the left end and sliding support at the right end) are also shown as follows:

![2](https://live.staticflickr.com/65535/53357402471_b3ec689796_b.jpg)


{{< columns >}}
Looking at the cross-section...

- Centroid position: Cy = -26.55 mm, Cz = 94.5 mm
- Moment of inertia in y-axis: Iy = 5101270.5 mm^4
- Moment of inertia in z-axis: Iz = 890980.8 mm^4
- Cross-sectional area: A = 865.125 mm^2

<--->

![3](https://live.staticflickr.com/65535/53357625098_4d07174430.jpg)
{{< /columns>}}


To analyze the results obtained in NX, it will be necessary to calculate the analytical values. For the boundary conditions of the problem (fixed support at the left end and sliding support at the right end), the calculation of the critical load for a beam with Euler-Bernoulli elements is given by:

![4](https://live.staticflickr.com/65535/53357625093_8b11c7078f_b.jpg)

Where the inertia to be used is the smaller value, namely Iz. However, NX solves the problem with Timoshenko elements. The conversion of the load from Euler-Bernoulli elements to Timoshenko elements is given by:


{{< katex display>}}
P_{cr}^T = \frac{P_{cr}^{EB}}{1+\frac{P_{cr}^{EB}}{G \times A_s}} = \frac{P_{cr}^{EB}}{1+\frac{P_{cr}^{EB}}{ \frac{E \times A \times K_s}{2 (1+\upsilon)} }}
{{< /katex >}}

G and As are given by:

{{< katex display>}}
G = \frac{E}{2(1+\upsilon)} \quad \, \quad A_s = A \times K_s
{{< /katex >}}

The value of Ks can be obtained in NX, within the properties of the section created in the fem file for 1D elements, and it is equal to 0.32592771.


## 2 1D Elements

### 2.1. Modeling & Meshing

In the NX software, the modeling begins with the creation of a part. To model the beam with 1D elements, simply draw a sketch composed of a line with a length of L = 1445 mm oriented along the X-axis direction. Afterwards, the FEM file is created. In this file, the finite element mesh is generated. 

![4](https://live.staticflickr.com/65535/53357402451_33d5094c17_b.jpg)

To obtain the mesh, a mesh collector is required, in this case, a Beam Collector of type PBEAML. Within the properties of the PBEAML, define the section type (CHAN) and its parameters (base, height, and section thickness). Once the section is defined, it's possible to analyze its properties such as area, centroid, moments of inertia, shear correction factors, among others, calculated by the software. The material of the beam is also defined within the PBEAML. For this purpose, a material is created with the desired Young's modulus, yield stress, and Poisson's ratio.

The mesh elements are defined as CBeam elements (beam elements). Within the mesh properties, establish the orientation of the section and its position relative to the beam line. The aim is to ensure that the beam line coincides with the centroid of the section.

![5](https://live.staticflickr.com/65535/53357735684_2f9acd1133_z.jpg)

Finally, the sim file is created. In this file, start by defining the solution type. To perform the linear stability analysis in order to obtain the instability modes, the [SOL 105 Linear Buckling solution](https://docs.plm.automation.siemens.com/data_services/resources/nxnastran/10/help/en_US/tdocExt/pdf/User.pdf) is used.

Within the sim file, the boundary conditions and the loads of the problem are applied. In this specific problem, the beam is fixed at the left end and has a sliding support, along with an applied load, at the right end.

These conditions are applied at the nodes. Hence, the node at the left end is fixed, preventing both rotations and translations, while the node at the right end is constrained to allow translation only along the X-axis (degree of freedom 1 in NX) and to permit rotation along the Y and Z axes (degrees of freedom 5 and 6 in NX). At the node on the right end, a reference load (1 kN) is applied, directed along the negative X-axis (compressive load).

![6](https://live.staticflickr.com/65535/53357735669_86e3de31f8_c.jpg)

Once the solution type, constraints, and applied loads are defined, the solution to the problem is obtained. This involves running the analysis within the software to calculate the response of the structure under the specified conditions. The software will then provide results such as critical loads, instability modes, deformations, stresses, or any other relevant outputs based on the defined parameters and boundary conditions.


### 2.2. Linear Stability Analysis in 1D Beam

Executing the Solve command in NX yielded the following results for the first 10 instability modes.

| Mode |    Instability Load   (kN) |
|------|----------------------------|
| 1    | 1631.13                    |
| 2    | 4385.45                    |
| 3    | 7710.4                     |
| 4    | 11029.3                    |
| 5    | 11361.5                    |
| 6    | 13018.2                    |
| 7    | 16547.2                    |
| 8    | 18614.2                    |
| 9    | 20274.7                    |
| 10   | 21598.9                    |

The critical instability load for the 1D model corresponds to the smallest instability load obtained in NX, which is 1631.13 kN (mode 1). The instability modes present in the 1D model's solution correspond to global instability modes (beam mode - axis buckling).

![7](https://live.staticflickr.com/65535/53357624968_87379142a2_z.jpg)

The beam exhibits rotation about the Z-axis, which has the smallest moment of inertia. Subsequently, it is reasonable to calculate the analytical value.

Starting with a beam consisting of Euler-Bernoulli elements:

{{< katex display >}}
P_{cr}^{EB} = 2.05 \times \frac{\pi^2 \times E \times I}{L^2} = 2.05 \times \frac{\pi^2 \times 200 \times 10^9 \times 890980.8 \times 10^{-12}}{1.445^2} = 1726.7 kN \\
{{< /katex >}}

Converting to Timoshenko elements...

{{< katex display>}}
P_{cr}^T = \frac{P_{cr}^{EB}}{1+\frac{P_{cr}^{EB}}{ \frac{E \times A \times K_s}{2(1+\upsilon)}   }} =  \frac{1726 \times 10^3}{1+\frac{1726 \times 10^3}{ \frac{200 \times 865.125 \times 0.3259 \times 10^3}{2(1+0.3)}   }} = 1682.3 kN
{{< /katex >}}

The relative error is given by:

{{< katex display>}}
e = \frac{\left| P_{cr}^T - P_{cr}^{NX} \right|}{P_{cr}^T } \times 100\% = \frac{1682.3 - 1631.13}{1682.3} = 3\%
{{< /katex >}}

The critical instability load obtained in NX has an error of 3% compared to the analytical value.

## 3 2D Elements

### 3.1. Modeling & Meshing

In the part file, a sketch is created in the YZ plane depicting the mid-section thickness line. An extrusion of length L is made along the X-plane from this sketch.

![30s](https://live.staticflickr.com/65535/53357402386_a345b61c96_b.jpg)

In the fem file, the mesh is generated using a PSHELL-type mesh collector. Within the properties of the PSHELL, the material properties (desired Young's modulus, yield stress, and Poisson's coefficient) are defined, and the section thickness (t=2.25mm) is inserted. The 2D mesh elements are defined as CQUAD4 (quadrilateral elements).

After obtaining the mesh, rigid elements need to be introduced at the ends. To do this, two nodes are created, one at each end of the beam, with Y and Z coordinates corresponding to the centroid of the section. Using the 1D connection command, a rigid element is created at each end, connecting the nodes present at each end.

![kdks](https://live.staticflickr.com/65535/53357624948_8d4f5b68a4.jpg)

In the sim file, the process is similar to what was described for 1D elements. Boundary conditions and constraints are applied to the nodes previously created at the ends passing through the centroid of the section.

![mm](https://live.staticflickr.com/65535/53357859145_fa236caffd_z.jpg)

Once the solution type, constraints, and applied loads are defined, the solution to the problem is obtained.

### 3.2. Linear Stability Analysis in 2D Beam