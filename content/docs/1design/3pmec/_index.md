---
weight: 3
bookHidden: false
title: "Mechanical Design"

---

# **How Much Can You Lift?**

## 1 Double Girder Crane for Industrial Application

### 1.1. Description and Context

This project was carried out within the scope of the Mechanical Design Course, part of my M.Sc journey. It aims to dimension and study fundamental structural components of a double girder suspension crane. Bridge cranes are frequently used equipment in the industry, especially in metallurgy, playing a pivotal role and being deployable both indoors and outdoors.
A bridge crane is a piece of equipment that, despite not being highly complex, encompasses all the elements that warrant study, allowing for the application of theoretical concepts applied to a real-world scenario.

### 1.2. Goals

This project aims to conduct the dimensioning and study of fundamental structural components. The sizing will be executed in accordance with applicable standards and demonstrated through analytical calculations and finite element models. In addition to the structural elements of the equipment, fundamental mechanical components of the system, including welded and bolted connections, will also be sized. Coupled with the dimensioning aspect is the economic factor, aiming to minimize costs while ensuring the safety of the entire structure

### 1.3. Functionalities


Bridge cranes are used for lifting and moving heavy loads. The structural and mechanical components to be sized are:
- Resistant girders, consisting of a reinforced box profile;
- Trolley frame;
- Trolley shaft;
- Driving and driven wheels;

The selected structural and mechanical components are:
- Hoist trolley;
- Bearings;
- Gear motor.

Main components:
- The trolley frame is located at the ends of the resistant girders. Inside it, the mechanism for the crane's movement is situated, powered by a gear motor. The wheels move along tracks, following well-defined paths.
- Resistant girders are crucial structural components in this type of equipment. They are the elements that endure the most stress caused by the weight of the load being transported.
- The hoist trolley has a translational motion along the resistant girders, allowing for the transport of the load perpendicular to the crane's direction of movement. It facilitates the lifting of the load to be transported.

![coolsa](https://live.staticflickr.com/65535/53347998632_c730c7c499_z.jpg)

{{< details "**In Portuguese:** Main components of a double girder crane  - (click to expand)" close >}}
![li2434n123ea412311421r](https://live.staticflickr.com/65535/53348870766_ac6841fe94_c.jpg)
{{< /details >}}


### 1.4. Design Specifications
#### 1.4.1. Design Requirements
The following requirements were considered for this project:
- Nominal load of 8 tons;
- Span between wheels of 10 meters;
- Workshop environment;
- Operating temperatures between 0 and 40 degrees Celsius;
- 8 hours of operation per day;
- Maximum translation speed of 25 m/min.

#### 1.4.2. Project Constraints

- The decision was made to design a bridge crane for an enclosed environment.

### 1.5. Standards, Codes, and Regulations

In the completion of this work, the FEM Standards (European Federation of Materials Handling), particularly Chapter II, and Eurocode 3 were utilized. The FEM standards address specific issues related to structures and handling equipment, including cases such as bridge cranes and gantry cranes.

### 1.6. Approach

A study was conducted on various existing construction solutions available in the market for this type of equipment. For the girders, a box-type section was chosen with a constant thickness of 10mm, made of AISI 1095 steel, and measuring 10m in length. These girders consist of 4 steel plates welded together with a 5mm thick welding bead. The rails are positioned on the girders, not aligned with the vertical plane passing through the girder's centroid, as this alignment would create a high bending moment on the upper plate of the girder. The connection between the girders and the trolley frame is established using 6 bolts M36x4 CR 10.9 and welding with a 10mm thick bead.

![saassa](https://live.staticflickr.com/65535/53349230049_69f514c133_w.jpg)

The chosen hoist trolley is the STAHL - SH 5020, which has a wheelbase of 1m and a mass of 755 kg. The following data pertains to the assembly composed of the two girders, two trolley frames, and the hoist trolley:

- Weight: 1229.3 kg
- Maximum width: 3.4 m
- Maximum length: 10.14 m

### 1.7. Manufacturing and Assembly Processes

The steel plates are obtained through a cold rolling process. Their surface roughness with an arithmetic mean deviation is 4.8 micrometers. The geometric tolerance concerning the thickness of these components is {{< katex >}}\pm{{< /katex >}}0.1 mm.

![saas2sa](https://live.staticflickr.com/65535/53349139963_c077712bdd_z.jpg)

For the resistant girders:

- 1. Welding of the two web plates to the bottom flange.
- 2. Welding of the internal transverse reinforcements. The end reinforcements of the girder, which will function as covers, will be welded in a manner that the welding beads remain inside the girder.
- 3. Welding of the top flange.
- 4. Welding of the rail.
- 5. Welding of the lateral reinforcements.
- 6. Welding of the base fixing.



The welding for the first, third, and fourth steps will be conducted using the Submerged Arc Welding process. This method is advantageous due to the substantial length of the welding bead, allowing for automation of the equipment carrying out the operation. The welding for the remaining steps will be executed using TIG (Tungsten Inert Gas) welding. The required equipment will be operated by a skilled worker.

### 1.8. Costs


The price of AISI 1095 Q&T steel is approximately €1/kg. Considering the girders weigh around 346.6 kg, their cost will be approximately €346.6. The labor cost is around €9/m, and the analyzed structure totals 53.2m, amounting to approximately €478.8.

### 1.9. General Simplifications

Only the main components were designed, namely the girders. The trolley frames and the remaining support structure were not designed. The studied overhead crane was interior, simplifying the analysis, as exterior conditions would introduce wind forces. For the sizing, it was considered that the hoist trolley was positioned in the middle of the girders (longitudinally), as this situation causes the greatest deformations.

## 2 Calculations

### 2.1. Material

Due to the high stresses to which the box girder of the overhead crane is subjected, it's necessary to select a structural steel with considerable yield strength to ensure the component meets service conditions and to minimize stress concentration and fatigue issues. With this in mind, the following steel with a tempering and tempering treatment was chosen:

| S_u      | S_y     | E       | G       | \nu     |
|----------|---------|---------|---------|---------|
| 1210 MPa | 772 MPa | 210 GPa | 81 GPa  | 0.3     |

 ### 2.2. Dimensioning Criteria 

 #### 2.2.1. Rigidity Criterion

 The box girders, with a length of 10 m, must meet this criterion from EN 1993-6:2007, where the maximum allowable deflection is given by:

 {{< katex display >}}
 \delta_{z,max} = \frac{L}{600} = 16.7 \text{mm}

 {{< /katex >}}
 To meet this criterion, an initial H/L ratio of 2.2 was chosen.


 #### 2.2.2. Mechanical Strength Criterion

The mechanical strength criterion was defined using the Pugsley criterion with the specified coefficients n = 2,08.

![saassa](https://live.staticflickr.com/65535/53349357450_366d636c17.jpg)

### 2.3. Beam Sizing

#### 2.3.1. Static Verification

To size the beam, an analytical model was developed considering two vertical loads corresponding to the nominal load and the weight of the trolley. The value of each of these two forces was previously defined according to the FEM standards, already multiplied by the dynamic coefficient and the amplification coefficient. The value of each force is 26.7 kN and they are spaced 1 meter apart. Additionally, the weight of the beam itself, which is not negligible, is represented as a distributed load. To verify the stiffness criterion, an H/L ratio of 2.2 was initially chosen.
Using tables of maximum deflection, employing the superposition method and also considering the load P equivalent to 26700N and the load application point (offset) at x = 4.5 m, the following expression for the beam's maximum deflection is obtained:

 {{< katex display >}}
y_{\text{max}} = 2 \left[ \left( \frac{P \times b}{6 \times E \times I_z \times 10} \right) \times \left( \left( 5^3 - \left( 10^2 - b^2 \right) \times 5 \right) \right) - \frac{5 \times w \times 10^4}{384 \times E \times I} \right]
 {{< /katex >}}

 Next, an expression for a given moment of inertia around z, Iz, was obtained as a function of the section's height H.

 {{< katex display >}}
 I_z = \frac{2}{10^{12}} \left[\left(10 \times (2H - 20)^3\right) + \left({2L \times 10^3}\right)\right] + 10L(H - 5)^2 
{{< /katex >}}

The distributed loading, w, was defined using the density of the chosen material ({{< katex >}}\rho{{< /katex >}}) = 78450 N/{{< katex >}}m^3{{< /katex >}}:

{{< katex display >}}
w = \frac{Volume \times \rho}{L} = 4.564H - 31.38
{{< /katex >}}

Upon solving the equation for maximum deflection, an expression in terms of H was obtained. From the graph, a value of H = 260 mm was selected, corresponding to L = 118.2 mm, thereby fulfilling the rigidity criterion.

![saass12121a](https://live.staticflickr.com/65535/53348060477_57f703bfeb_w.jpg)

At present, the computation of the Von Mises equivalent stress has become feasible. This involves determining both the shear stress resulting from torsion and the shear stress attributed to it.

{{< katex display >}}
\tau_V = \frac{V \times Q}{I_z \times b} = \frac{27170 \times 2 \times (0.125 \times 0.0025 + 0.255 \times 0.0023)}{0.00052 \times 0.02} = 3.234 \times 10^6 \text{Pa} \\

\\ \tau_T = \frac{T}{2 \times A_m \times t} = \frac{2250 \times 2}{2 \times (0.1664 \times 0.510) \times 0.1} = 2.651 \times 10^6 \text{Pa} \\

\\ \sigma_M = \frac{M_c}{I_z} = \frac{131660 \times 0.26}{0.00052} = 6.637 \times 10^7 \text{Pa} \\
{{< /katex >}}

Subsequently:

{{< katex display >}}
\sigma_{VM} = \sqrt{\sigma_M^2 + 3\left(\tau_V^2 + \tau_T^2\right)} = 6.723 \times 10^7  \text{Pa}
{{< /katex >}}

Having the safety factor:

{{< katex display >}}
n = \frac{S_y}{\sigma_{VM}} = \frac{772 \times 10^6}{6.723 \times 10^7} = 11.482 > 2.08
{{< /katex >}}

#### 2.3.2. Fatigue Verification

The minimum stress is determined by the weight of the beam and the winch car itself (considering the winch car unloaded). Conversely, when the winch car is loaded (the scenario considered for calculating the static safety factor), the corresponding stress will be at its maximum. We can derive the ratio between the minimum and maximum stresses by calculating the minimum stress for the unloaded car. Repeating this process for the calculation of the Von Mises stress for the unloaded load case yields.

{{< katex display >}}
R = \frac{\sigma_{VM_0}}{\sigma_{VM}} = \frac{1.106 \times 10^7}{6.723 \times 10^7} = 0.1645
{{< /katex >}}

The fatigue limit stress was determined to be 321.617 MPa through the calculation of Marin's factors.

{{< katex display >}}

n_{Goodman} = \frac{1}{\frac{\sigma_a}{S_e} + \frac{\sigma_m}{S_u}} = 8.355\\ \\

\\ n_{Yield} = \frac{1}{\frac{\sigma_a + \sigma_m}{S_y}} = 11.482\\
{{< /katex >}}

For a 25% overload (1.25 times the load) occurring 10% of the time and taking a factor of f = 0.858 figure 6-18 [of Shigley's Mechanical Design book](https://books.google.at/books/about/Shigley_s_Mechanical_Engineering_Design.html?id=B7wivgAACAAJ&redir_esc=y)

{{< katex display >}}
\sigma = \frac{1.25 \times \sigma_a}{1 - \frac{1.25 \times \sigma_m}{S_{ut} \times 10^6}} = 3.51 \times 10^7 \, \text{Pa}
{{< /katex >}}

{{< katex display >}}
a = \frac{(f \times S_{ut})^2}{S_e \times 10^6} = 3.35 \times 10^9
{{< /katex >}}

{{< katex display >}}
b = -\frac{1}{3} \times \log \left( \frac{f \times S_{ut}}{S_e \times 10^6} \right) = -0.391
{{< /katex >}}

{{< katex display >}}
\text{Cycles} = \left( \frac{\sigma}{a} \right)^\frac{1}{b} = 1.17 \times 10^5 \text{cycles}
{{< /katex >}}

### 2.4. Finite Element Analysis


#### 2.4.1. Beam Analysis

For the analysis of the resilient beam using finite element methods, the commercial software Solidworks was employed. The numerical model comprises a simply supported beam on the two hinges of the plate to be bolted to the winch's head, with a load of 27.6 kN applied at the geometric location where the winch car wheels would be. The beam's self-weight is also considered.


![saa234ss12121a](https://live.staticflickr.com/65535/53348105597_f2355695a3.jpg)

In terms of the developed stresses, at the neutral axis (which in this case is the centroidal axis), the Von Mises equivalent stress is zero or close to zero, as expected. It is also inferred that the highest stresses exist at the outermost fibers of the section, where there is a maximum tensile stress in the lower outer fiber and a compressive stress in the upper outer fiber.

Both these stresses are considerably lower than the material's yield stress, ensuring the specified safety factor.

![saa234ss112323112121a](https://live.staticflickr.com/65535/53348105582_74015a2bab.jpg)

The maximum deflection due to loading measures 9.584 mm and is positioned at the midpoint of the beam span. Comparing this with the analytically obtained deflection of 11.5 mm, there is a relative error of 16.7%. This error arises from the consideration of internal reinforcements within the beam and, simultaneously, from the boundary conditions not being applied at the beam ends but rather at the hinge point.

#### 2.4.2. Assembly Analysis

For the analysis of stresses arising from a bolted connection, a finite element model was developed, considering only half of the beam due to symmetry effects. The resilient beam is connected to a rigid plate via a bolted connection. The applied load is 26.7 kN, and the beam's self-weight is also accounted for in the simulation.

![saa231314ss123112121a](https://live.staticflickr.com/65535/53348105567_51a8fd8b91_c.jpg)

Boundary conditions involve restricting rotation and translation at the lower base of the plate to which the beam is bolted, and considering symmetry in the beam, allowing for the modeling of only half of it.

For defining the connection at each bolt, a pre-load of 50900 N was specified, corresponding to an M36 bolt of grade 10.6.

![123132saa231314ss123112121a](https://live.staticflickr.com/65535/53349426705_940491ca28_z.jpg)

![saa231314s421415s12315312121a](https://live.staticflickr.com/65535/53349300039_cd16875d00_z.jpg)

The highest Von Mises equivalent stresses are located near the bolt head regions, measuring around 323 MPa, ensuring the mechanical strength criteria specified for the project.

Additionally, this model presents maximum stresses at the hole edge of the bolted plate, which increase as the mesh around it becomes finer. In this scenario, the existence of a geometric singularity (an edge without area) leading to very high stresses was considered. However, this singularity was disregarded in the analysis.

![saa231314s421000415s123112121a](https://live.staticflickr.com/65535/53348977501_3a31d6d2b0_c.jpg)

The maximum deflection in the deformed configuration of the beam was 4.27 mm, approximately half of the deflection obtained in the previous finite element model (simply supported beam).

### 2.5. Welded Joint

In this section, the aim is to study the welded connection of the internal forces within the beam and the welded connection of the bolted fixation plates along with the welding between the plates.

![saa231314s421000415s123112121a](https://live.staticflickr.com/65535/53348992386_ae5d238363_h.jpg)

The intention is to analyze the welded connection concerning static, yield, and fatigue aspects. Both components to be welded are made of the same material (AISI 1095 HR Steel).

#### 2.5.1 External Welding

The welding performed has a height, h, of 5 mm. Therefore, the throat height, a, is such that {{< katex >}}a = \frac{h}{\sqrt{2}} = 3.54 \text{mm} {{< /katex >}}. 

The procedure for calculating the weld beads in the three cases is similar, involving the computation of the unit second moment of area (Ju), the moment of inertia (Is) and the weld area (As). For the calculation of the unit second moment of area for more complex sections, it is feasible to compute it by dividing the section into several rectangles and selecting the appropriate expression from the table (Table 9-1 and 9-2 in [Shigley's book](https://books.google.at/books/about/Shigley_s_Mechanical_Engineering_Design.html?id=B7wivgAACAAJ&redir_esc=y)) corresponding to the specific case.

{{< katex display >}}
J_u = \frac{b \times d^2}{2} = \frac{10 \times 0.17642^2}{2 \times 2} = 0.1556 \text{m}^3
{{< /katex >}}


Given this information, the moment of inertia is determined by the product of the throat height, denoted as a, and the unit second moment of area:

{{< katex display >}}
J = J_u \times a = 5.501 \times 10^{-4} \text{m}^4
{{< /katex >}}

As outlined in Table 9.1 [in Shigley's book](https://books.google.at/books/about/Shigley_s_Mechanical_Engineering_Design.html?id=B7wivgAACAAJ&redir_esc=y), the formula for the throat area is expressed as follows:

{{< katex display >}}
A_s = 0.707 \times h \times d = 0.707 \times 20 \times 0.005 = 0.0707 \text{m}^2
{{< /katex >}}

With this data, it is possible to calculate the primary shear stress:

{{< katex display >}}
\tau_V = \frac{F}{A_s} = \frac{31350}{0.0707} = 4.43 \times 10^5 \text{Pa}
{{< /katex >}}

In the study of secondary shear stress, the component attributed to the torsional moment and the bending moment is calculated, with the former being represented by the following expression.

{{< katex display >}}
\tau_T = \frac{T_c}{J} = \frac{2250 \times 0.082}{5.501 \times 10^{-4}} = 3.608 \times 10^5 \text{Pa}

{{< /katex >}}

In the case of secondary shear stress due to the bending moment, it is given that:

{{< katex display >}}
\tau_M = \frac{M_r}{I} = \frac{131660 \times 5}{\frac{10^3}{6} \times 0.0035} = 1.116 \times 10^6 \text{Pa}
{{< /katex >}}


Hence, the summation of each of these components enables the assessment of the stress value induced by the moments.

{{< katex display >}}
\tau_{MT} = \tau_M + \tau_T = 1.116 \times 10^6 + 3.608 \times 10^5 = 1.478 \times 10^6 \, \text{Pa}
{{< /katex >}}

By calculating the total stress induced by the moments and the shear force, it is concluded that:

{{< katex display >}}
\tau = \sqrt{\tau_{MT}^2 + \tau_V^2} = \sqrt{1.478 \times 10^6 + 4.43 \times 10^5} = 1.543 \times 10^6 \, \text{Pa}
{{< /katex >}}

To conclude the study of welded connections between the vertical and horizontal plates, it is proposed to evaluate the static safety coefficient and compare it with the initially defined value using the Pugsley method (n = 2.08). This comparison aims to verify if the obtained value surpasses the initially set one.

{{< katex display >}}
\text{Nestatica} = \frac{0.4 \times S_y}{\tau} = \frac{0.4 \times 460 \times 10^6}{1.543 \times 10^6} = 119.25
{{< /katex >}}

**OK!**

#### 2.5.2 Internal Welding

It was mentioned that the purpose of the internal vertical reinforcements is to prevent localized deformation of the beam when subjected to the forces resulting from the equipment's operational regime. They also serve as a means to provide additional stiffness to the box beam. The shear stresses present in the two webs of the box beam, during loading and translation of the beam, could lead to detrimental localized deformations affecting the equipment. It was recommended that vertical transverse reinforcements should also be applied to areas where the beam is supported and bolted to the "headstock".

According to the CMAA (Crane Manufacturers Association of America) specification for overhead cranes, the spacing between vertical reinforcements should not exceed:


{{< katex display >}}
a = \frac{350}{\sqrt{v} \times t} = \frac{350}{\sqrt{0.333} \times 0.3937} \approx 6065.5 \text{mm}
{{< /katex >}}

- a: Spacing between vertical reinforcements (in)
- v: Maximum shear stress in the section (ksi) - 2.3E6 Pa = 0.333 ksi
- t: Thickness of the beam's web (in) - 10 mm

It was determined to install 11 reinforcements along the length of the beam, with two of them acting as caps to seal the structural section.