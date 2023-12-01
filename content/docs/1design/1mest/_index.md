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
\epsilon = \frac{\left| P_{cr}^T - P_{cr}^{NX} \right|}{P_{cr}^T } \times 100\% = \frac{1682.3 - 1631.13}{1682.3} = 3\%
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

The command "Solve" in NX yielded the following results for the first 10 modes of instability:


| Mode | Instability Load (kN) |
|------|-----------------------|
| 1    | 61.43                 |
| 2    | 61.7                  |
| 3    | 65.95                 |
| 4    | 66.14                 |
| 5    | 72.56                 |
| 6    | 75.12                 |
| 7    | 77.3                  |
| 8    | 77.43                 |
| 9    | 81.44                 |
| 10   | 82.4                  |

In NX, it's observed that all these modes are local modes. The first mode corresponds to the lowest instability load associated with a local mode, hence {{< katex >}}P_L{{< /katex>}} = 61.43 kN.

![asd](https://live.staticflickr.com/65535/53356528047_bd6e4b4ec3_q.jpg)

To find the first global instability mode, it's necessary to increase the number of modes identified by NX. Using the critical load value for Timoshenko beam elements (1682.3 kN) as a reference, the search is directed towards a global mode close to this load value. The following mode was found:

![asdmsa3](https://live.staticflickr.com/65535/53356527972_71c73818f0_z.jpg)

![dsaao2](https://live.staticflickr.com/65535/53356527967_96ed3aa7c0_c.jpg)

The identified mode is a mixed mode, displaying both axis and plates buckling. However, it was the sole mode exhibiting significant axis buckling. Thus, considering the lowest load associated with a global mode, the load corresponding to this mode, {{< katex >}}P_G{{< /katex >}} = 1660.79 kN, is regarded as the minimum instability load associated with a global mode. Since {{< katex >}}P_L{{< /katex >}} < {{< katex >}}P_G{{< /katex >}}, {{< katex >}}P_L{{< /katex >}} represents the critical instability load.

{{< hint example >}}
Relative error: It is observed that the lowest load associated with a global instability mode in NX exhibits an error of 1.28% compared to the analytical value.
{{< /hint>}}

{{< katex display>}}
\epsilon = \frac{\left| P_{cr}^T - P_{cr}^{NX} \right|}{P_{cr}^T } \times 100\% = \frac{1682.3 - 1660.79}{1682.3} \times 100 \% = 1.28\%
{{< /katex>}}

In order to find a purely global (non-mixed) mode, it's necessary to increase the length, L, of the beam. As the length, L, increases, the plate buckling present in the mixed mode decreases until the mode transitions to purely global. For instance, considering a 3L (mode 134):

![sdamkm222](https://live.staticflickr.com/65535/53356527962_14cb04324b_h.jpg)

Now, for a 5L (mode 24):

![123dd](https://live.staticflickr.com/65535/53357624893_d081aed907_b.jpg)

As the length increases, the wrinkling of the plate decreases.

### 3.3. Determination of {{< katex >}}L_{GL} {{< /katex >}}

Through sensitivity analysis, the length (L) of the 2D beam was varied to make {{< katex >}}P_G{{< /katex >}} = {{< katex >}}P_L{{< /katex >}}. Modifying the length required changes in the part file and updating the .fem and .sim files. An initial estimate of 5L was used, adjusting it based on whether {{< katex >}}P_G{{< /katex >}} was greater or lesser than {{< katex >}}P_L{{< /katex >}}. The value of {{< katex >}}P_G{{< /katex >}} significantly decreases as L increases (considering the {{< katex >}}P_{cr}{{< /katex >}} formula for Euler-Bernoulli elements). On the other hand, {{< katex >}}P_L{{< /katex >}} shows minimal variation with changes in L. Therefore, if PG exceeds {{< katex >}}P_L{{< /katex >}}, L is increased; otherwise, L is decreased. The following table summarizes the iterative process:

| Iteration | Length | {{< katex >}}P_{Global}{{< /katex >}} (kN) | {{< katex >}}P_{Local}{{< /katex >}} (kN)        |
|-----------|--------|--------------|--------------------|
| 1         | 5L     | 68.5 - mode 24| 60.1 - mode 1     |
| 2         | 5.25L  | 62.227 - mode 13 | 60.09 - mode 1 |
| 3         | 5.5L   | 56.7257 - mode 1 | 60.09 - mode 2 |
| 4         | 5.3L   | 61.0645 - mode 9 | 60.09 - mode 1 |
| 5         | 5.4L   | 58.8354 - mode 1 | 60.09 - mode 2 |
| 6         | 5.35L  | 59.9 - mode 1  | 60.09 - mode 2 |
| 7         | 5.34L  | 60.1577 - mode 3 | 60.09 - mode 1 |
| 8         | 5.343L | 60.04 - mode 1 | 60.09 - mode 2 |
| 9         | 5.342L | 60.113 - mode 3 | 60.09 - mode 2 |

Hence: {{< katex >}}L_{GL}{{< /katex >}} = 5,342L {{< katex >}}\times{{< /katex >}} 1445 = 7719,9 mm

For this length, the following modes are obtained:

- Mode 1, P = 60.0936 kN: Mixed mode

![sadjiajd3](https://live.staticflickr.com/65535/53357859075_43a70e7ba6_w.jpg)

- Mode 2, P = 60.0937 kN: Local mode

![dsaji22](https://live.staticflickr.com/65535/53357402276_a8ee6d96f8_z.jpg)

- Mode 3, P = 60.113 kN: Global mode

![l03](https://live.staticflickr.com/65535/53357735484_72555565cd_c.jpg)

![032o](https://live.staticflickr.com/65535/53357402281_c115d25939_c.jpg)

The local and global modes are separated by 0.0193 kN = 19.3 N, which corresponds to a relative difference of 0.032% between the two. Therefore, they can be considered equal ({{< katex >}}P_G{{< /katex >}} = {{< katex >}}P_L{{< /katex >}}). Comparing with the analytical values:

{{< katex display>}}
P_{cr}^{EB} = 2.05 \times \frac{\pi^2 \times E \times I}{5.432 \times L^2} = 60.507 kN
{{< /katex >}}


{{< katex display>}}
\text{Timoshenko}: P_{cr}^T = \frac{P_{cr}^{EB}}{1+\frac{P_{cr}^{EB}}{G \times A_s}} = \frac{P_{cr}^{EB}}{1+\frac{P_{cr}^{EB}}{ \frac{E \times A \times K_s}{2 (1+\upsilon)} }} = 60.445 kN 
{{< /katex >}}

{{< katex display>}}
\epsilon = \frac{\left| P_{cr}^T - P_{cr}^{NX} \right|}{P_{cr}^T } \times 100\% = \frac{60.445 - 60.0937}{60.445} \times 100\% = 0.58\%
{{< /katex >}}

## 4 Linear Static Analysis

To perform the linear static analysis, a new solution is set up within the previously created sim files for both 1D and 2D beam elements, utilizing Solver 101 Linear Statics - Global Constraints.

For this analysis, aligning the compressive force to achieve {{< katex >}}P_G{{< /katex >}} = {{< katex >}}P_L{{< /katex >}} was imperative. Given the marginal difference between {{< katex >}}P_G{{< /katex >}} and {{< katex >}}P_L{{< /katex >}}, an averaged force value of P = 60.10 kN was adopted.

Following the meticulous definition of solution parameters, constraints, and applied loads, the problem underwent resolution. Execution of the Solve command enabled the observation of Von-Mises stresses within NX.

In the context of the 1D element beam, the resulting Von-Mises stresses were as follows:

![dadcd3](https://live.staticflickr.com/65535/53357624833_26901e053d_z.jpg)

The maximum value identified is {{< katex >}}\sigma_{max}{{< /katex >}} = 69.521 MPa, significantly below the yield stress {< katex >}}\sigma_y{{< /katex >}} = 700 MPa

Safety factor: n = {{< katex >}} \frac{\sigma_y}{\sigma_{max}} = 10.07 {{< /katex>}}

In the context of the 2D element beam, the resulting Von-Mises stresses were as follows:

![l122112](https://live.staticflickr.com/65535/53357859035_b9b991d8ce_z.jpg)

The maximum value identified is {{< katex >}}\sigma_{max}{{< /katex >}} = 72.58 MPa, still significantly below the yield stress {{< katex >}}\sigma_y{{< /katex >}} = 700 MPa

Safety factor: n = {{< katex >}} \frac{\sigma_y}{\sigma_{max}} = 9.64 {{< /katex>}}


{{< hint tip>}}
An analytic calculation for the Von-Mises stress can be performed using the formula:
{{< hint /tip>}}

{{< katex display>}}
\sigma_{VM}=\sqrt{ (\sigma_M + \sigma_N)^2 + 3(\tau_T + \tau_V)^2 }
{{< /katex>}}

In this scenario, there's only a compressive force applied at the centroid, meaning there's no applied bending moment ({{< katex >}}\sigma_M{{< /katex >}}=0) and no shear stresses present (zero bending moment and transverse shear). Thus:

{{< katex display>}}
\sigma_{VM}= \sigma_M = \frac{P}{A} = \frac{60.10 \times 10^3}{865.125 \times 10^{-6}}
{{< /katex>}}

Safety factor: n = {{< katex >}} \frac{\sigma_y}{\sigma_{VM}} = 10.08 {{< /katex>}}

Therefore, the Von-Mises stress analysis suggests a uniform stress distribution along the beam, although the NX software exhibits some fluctuations. Additionally, the stress does not seem to depend on the length L of the beam. In the case of 2D elements, larger oscillations occur at the ends, while outside these areas (highlighted in yellow in the previous image), the stress approaches the analytical value. For the 1D elements, oscillations are minimal, and stress values closely match the analytical values. The relative error between the maximum stress in the beams and the analytical stress is:

{{< katex display>}}
- 1D: \epsilon = \frac{\left| 69.521 - 69.47 \right|}{69.47 } \times 100\% = 0.073 \%
{{< /katex>}}

{{< katex display>}}
- 2D: \epsilon = \frac{\left| 72.58 - 69.47 \right|}{69.47 } \times 100\% = 4.48 \%
{{< /katex>}}

Evidently, the yield stress is far from being exceeded, with safety factors close to 10.

## 5 Nonlinear Analysis

To generate load-displacement plots in a nonlinear regime, it was necessary to create an initial geometric imperfection shaped according to the local instability mode for a beam length of 0.7{{< katex>}}L_{GL}{{< /katex>}} = 0.7 {{< katex>}}\times{{< /katex>}} 5.342L = 5.44 m. This involved modifying the part file and updating the .fem and .sim files for a beam length of 5.44 m. The displacement corresponding to the first local mode was determined. Using these values, a local geometric imperfection was introduced into a new fem file, utilized to create a new sim file for conducting a nonlinear analysis. Through this analysis, variations in applied force concerning the load and displacement changes across different increments were obtained:

![13ok33](https://live.staticflickr.com/65535/53356527837_26e7f9950b_c.jpg)

