---
weight: 3
bookHidden: false
title: "Industrial Girder Crane"

---

# **How Much Can You Lift?**

![h21e1e3](https://pwiworks.com/wp-content/uploads/single-girder-bridge-crane-1200x600.jpg)

## 1 Double Girder Crane for Industrial Application

### 1.1. Description and Context

Dimensioning and studying fundamental structural components of a double girder suspension crane can be in an interesting study framework. Bridge cranes are frequently used equipment in the industry, especially in metallurgy, playing a pivotal role and being deployable both indoors and outdoors. 

{{< hint important >}}
A bridge crane is a piece of equipment that, despite not being highly complex, encompasses all the elements that warrant study, allowing for the application of theoretical concepts in mechanical design engineering applied to a real-world scenario.
{{< /hint >}}

### 1.2. Goals

The aim is to conduct the dimensioning and study of fundamental structural components. The sizing will be executed in accordance with applicable standards and demonstrated through analytical calculations and finite element models. In addition to the structural elements of the equipment, fundamental mechanical components of the system, including welded and bolted connections, will also be sized. Coupled with the dimensioning aspect is the economic factor, aiming to minimize costs while ensuring the safety of the entire structure

### 1.3. Functionalities

{{< details "Structural and mechanical components - (click to expand)" close >}}
Bridge cranes are used for lifting and moving heavy loads. The structural and mechanical components to be sized are:
- Resistant girders, consisting of a reinforced box profile;
- Trolley frame;
- Trolley shaft;
- Driving and driven wheels;

The selected structural and mechanical components are:
- Hoist trolley;
- Bearings;
- Gear motor.
{{< /details >}}

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

- The decision was made to design a bridge crane for an enclosed / indoors environment.

### 1.5. Standards, Codes, and Regulations

In the completion of this work, the [FEM Standards (European Federation of Materials Handling)](https://www.fem-eur.com/), particularly Chapter II, and [Eurocode 3](http://eurocodes.jrc.ec.europa.eu/EN-Eurocodes/eurocode-3-design-steel-structures) were utilized. The [FEM standards](https://www.fem-eur.com/) address specific issues related to structures and handling equipment, including cases such as bridge cranes and gantry cranes.

### 1.6. Approach

A study was conducted on various existing construction solutions available in the market for this type of equipment. For the girders, a box-type section was chosen with a constant thickness of 10mm, made of AISI 1095 steel, and measuring 10m in length. These girders consist of 4 steel plates welded together with a 5mm thick welding bead. The rails are positioned on the girders, not aligned with the vertical plane passing through the girder's centroid, as this alignment would create a high bending moment on the upper plate of the girder. The connection between the girders and the trolley frame is established using 6 bolts M36x4 CR 10.9 and welding with a 10mm thick bead.

![saassa](https://live.staticflickr.com/65535/53349230049_69f514c133_w.jpg)

The chosen hoist trolley is the [STAHL - SH 5020](https://www.manualslib.com/products/Stahl-Sh-5020-25-11019601.html), which has a wheelbase of 1m and a mass of 755 kg. The following data pertains to the assembly composed of the two girders, two trolley frames, and the hoist trolley:

- Weight: 1229.3 kg
- Maximum width: 3.4 m
- Maximum length: 10.14 m

### 1.7. Manufacturing and Assembly Processes

The steel plates are obtained through a cold rolling process. Their surface roughness with an arithmetic mean deviation is 4.8 micrometers. The geometric tolerance concerning the thickness of these components is {{< katex >}}\pm{{< /katex >}}0.1 mm.

{{< hint info>}}
Cold rolling is a way to change the shape or reduce the thickness of metal sheets  while keeping the metal at room temperature or slightly above. It's done by passing the metal through rollers that apply pressure, making it thinner or changing its shape.{{< /hint>}}

![saas2sa](https://live.staticflickr.com/65535/53349139963_c077712bdd_z.jpg)

For the resistant girders:

- 1. Welding of the two web plates to the bottom flange.
- 2. Welding of the internal transverse reinforcements. The end reinforcements of the girder, which will function as covers, will be welded in a manner that the welding beads remain inside the girder.
- 3. Welding of the top flange.
- 4. Welding of the rail.
- 5. Welding of the lateral reinforcements.
- 6. Welding of the base fixing.

The welding for the first, third, and fourth steps will be conducted using the Submerged Arc Welding (SAW) process. This method is advantageous due to the substantial length of the welding bead, allowing for automation of the equipment carrying out the operation. The welding for the remaining steps will be executed using TIG (Tungsten Inert Gas) welding. The required equipment will be operated by a skilled worker.

{{< hint info>}}
TIG uses a non-consumable tungsten electrode, precise control, and shielding gas. Ideal for thinner metals and detailed work. Where SAW involves an arc between a continuously fed wire electrode and the workpiece, submerged under flux. Great for welding thicker materials efficiently in a single pass.{{< /hint>}}

### 1.8. Costs


The price of [AISI 1095 Q&T steel](https://www.azom.com/article.aspx?ArticleID=6561) is approximately €1/kg. Considering the girders weigh around 346.6 kg, their cost will be approximately €346.6. The labour cost is around €9/m, and the analyzed structure totals 53.2m, amounting to approximately €478.8.

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

To size the beam, an analytical model was developed considering two vertical loads corresponding to the nominal load and the weight of the trolley. The value of each of these two forces was previously defined according to the [FEM standards](https://www.fem-eur.com/), already multiplied by the dynamic coefficient and the amplification coefficient. The value of each force is 26.7 kN and they are spaced 1 meter apart. Additionally, the weight of the beam itself, which is not negligible, is represented as a distributed load. To verify the stiffness criterion, an H/L ratio of 2.2 was initially chosen.
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
{{< /katex >}}

{{< katex display >}}
\\ \tau_T = \frac{T}{2 \times A_m \times t} = \frac{2250 \times 2}{2 \times (0.1664 \times 0.510) \times 0.1} = 2.651 \times 10^6 \text{Pa} \\
{{< /katex >}}

{{< katex display >}}
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

The minimum stress is determined by the weight of the beam and the winch car itself (considering the winch car unloaded). Conversely, when the winch car is loaded (the scenario considered for calculating the static safety factor), the corresponding stress will be at its maximum. It can be derived the ratio between the minimum and maximum stresses by calculating the minimum stress for the unloaded car. Repeating this process for the calculation of the Von Mises stress for the unloaded load case yields.

{{< katex display >}}
R = \frac{\sigma_{VM_0}}{\sigma_{VM}} = \frac{1.106 \times 10^7}{6.723 \times 10^7} = 0.1645
{{< /katex >}}

The fatigue limit stress was determined to be 321.617 MPa through the calculation of Marin's factors.

{{< katex display >}}
n_{Goodman} = \frac{1}{\frac{\sigma_a}{S_e} + \frac{\sigma_m}{S_u}} = 8.355\\ \\
{{< /katex >}}

{{< katex display >}}
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

According to the [CMAA (Crane Manufacturers Association of America)](https://en.wikipedia.org/wiki/Crane_Manufacturers_Association_of_America) specification for overhead cranes, the spacing between vertical reinforcements should not exceed:


{{< katex display >}}
a = \frac{350}{\sqrt{v} \times t} = \frac{350}{\sqrt{0.333} \times 0.3937} \approx 6065.5 \text{mm}
{{< /katex >}}

- a: Spacing between vertical reinforcements (in)
- v: Maximum shear stress in the section (ksi) - 2.3E6 Pa = 0.333 ksi
- t: Thickness of the beam's web (in) - 10 mm

It was determined to install 11 reinforcements along the length of the beam, with two of them acting as caps to seal the structural section.

The welding of internal reinforcements is similar to that of welding between vertical and horizontal plates, with a height, h, of 5mm. Therefore, the throat height, a, is such that ar = {{< katex >}}\frac{h}{sqrt{2}}{{< /katex >}} = 3.54 mm.

The radius:

{{< katex display >}}
𝑟 = \sqrt{\left(\frac{0.5}{2}\right)^2 + \left(\frac{0.1564}{2}\right)^2} = 0.261 \, \text{m}
{{< /katex >}}

Replacing in the stress calculation:

{{< katex display >}}
\tau_{\text{T}} = \frac{2250 \times r}{J_{\text{u}} \times ar} = \frac{2250 \times 0.261}{0.00354 \times \frac{0.5 \times \left(3 \times 0.1564^2 + 0.5^2\right)}{6}} = 6.186 \times 10^6 \, \text{Pa}
{{< /katex >}}

In the case of stresses due to transverse force:

{{< katex display >}}
\tau_{\text{V}} = \frac{F}{A} = \frac{31350}{0.00354 \times (2 \times 0.5)} = 8.867 \times 10^6 \, \text{Pa}
{{< /katex >}}

The calculation of the angle between the stresses caused by the torsional moment and the transverse force involved using the following expression:

{{< katex display >}}
\cos \theta = \frac{0.25}{r} = 0.954 \Rightarrow \theta = \cos^{-1}(0.954) = 0.303 \, \text{rad} = 17.378^\circ
{{< /katex >}}

The expression used to calculate the angle between the stresses caused by the torsional moment and the transverse force was applied for substitution into the equation used to determine the resultant.

{{< katex display >}}
\tau_{\text{C}} = \tau_{\text{R}} = \sqrt{\tau_{\text{T}}^2 + \tau_{\text{V}}^2 + 2\tau_{\text{T}}\tau_{\text{V}}\cos(2\theta)} = 1.439 \times 10^7 \, \text{Pa}
{{< /katex >}}

For the safety factor calculation:

{{< katex display >}}
n_e = \frac{0.4 \times S_y}{\tau_{\text{C}}} = \frac{0.4 \times 460 \times 10^6}{1.439 \times 10^7} = 12.788
{{< /katex >}}

Based on this value, it is concluded that it exceeds the previously established value, prompting to conduct a further study of this welded joint.


#### 2.5.3. Bolted Joint Welding

For the study of the welded joint in the bolted fixing plate, I was considering a weld bead height of h = 10mm — higher than previously assigned in the previous welded joint to ensure a higher safety margin. As mentioned before, with more complex sections, it is necessary to divide them into more uniform and simpler sections. To do this, using tables 9-1 and 9-2 [of Shigley's Mechanical Design book](https://books.google.at/books/about/Shigley_s_Mechanical_Engineering_Design.html?id=B7wivgAACAAJ&redir_esc=y) were consulted to extract the values of Ju and Iu, respectively.

{{< columns >}}


![sdam](https://live.staticflickr.com/65535/53357465853_6747db4d94_c.jpg)
<--->
![dakoo](https://live.staticflickr.com/65535/53357465848_05f9de1f41_n.jpg)

{{< /columns >}}

{{< katex display >}}
I_u = \frac{0.244^3}{6} + 2 \times \frac{0.08^3}{6} + \frac{0.5964 \times 0.26^2}{2} + 2 \times \frac{0.180 \times 0.244^2}{2} = 0.0335 \, \text{m}^3

{{< /katex >}}

{{< katex display >}}
J_u = \frac{0.5964^3}{6} + 2 \times \frac{0.180^3}{6} + \frac{0.244 \times 0.180^2}{2} + 2 \times \frac{0.008 \times 0.26^2}{2} = 0.0418 \, \text{m}^3

{{< /katex >}}


Similarly to the calculations for previous welded joints:

{{< katex display >}}
\tau_{MT} = \tau_{T} + \tau_{M} = \frac{131775 \times 0.13}{\frac{0.0335}{\sqrt{2}} \times 0.01} + \frac{2250 \times \fra{0.5964}{2}}{\frac{0.0418}{\sqrt{2}} \times 0.01}
{{< /katex >}}

For the primary shear stress, it is critical to compute the applied shear force as well as the throat area of the weld:

{{< katex display >}}
F = 1150 \times 5 + 26700 = 32475 N \, \\
{{< /katex >}}
{{< katex display >}}
P_s = 2 \times 0.5964 + 2 \times 0.244 + 4 \times 0.18 + 4 \times 0.008 = 2.4328 m \, \\
{{< katex display >}}
{{< /katex >}}
A_s = 0.707 \times h \times P_s = 0.707 \times 0.101 \times 2.4328 = 0.0172 m^2 \, \\
{{< katex display >}}
{{< /katex >}}
{{< katex display >}}
\tau_V = \frac{F}{A_s} = \frac{32475}{0.0172} = 1.89 \times 10^6 \, \text{Pa} \, \\
{{< /katex >}}


Calculating the total stress due to moments and shear force:

{{< katex display >}}
\tau = \sqrt{\tau_{MT}^2 + \tau_V^2}
{{< /katex >}}

To conclude, let's evaluate the safety factor in this static corner weld and compare it with the value initially defined via the Pugsley method (n = 2.08) to verify if indeed the obtained value is higher.

{{< katex display >}}
n_{\text{static}} = \frac{0.4 \times S_y}{\tau} = \frac{0.4 \times 460 \times 10^6}{7.468 \times 10^7} = 2.464
{{< /katex >}}

It is OK! ✔️

This value isn't far from the initially assigned one, also being the lowest among all the welded joints. Therefore, moving on to the fatigue design, assessing the safety factor, which is expected to decrease. This conditions will be verified whether it still holds true — where the fatigue design of the weld bead on the plate to be bolted to the head is the determining factor. In the fatigue analysis, both mean and alternating load components are considered.

{{< katex display >}}
M_{alt} = \frac{131775 - 27970}{2} = 51902.5 \, \text{Nmm} \, \\
{{< /katex >}}
{{< katex display >}}
M_{med} = \frac{131775 + 27970}{2} = 79872.5 \, \text{Nmm} \, \\
{{< /katex >}}
{{< katex display >}}
T_{alt} = \frac{2250 - (2300 \times 0.0842)}{2} = 1028.2 \, \text{Nmm} \, \\
{{< /katex >}}
{{< katex display >}}
T_{med} = \frac{2250 + (2300 \times 0.0842)}{2} = 1221.8 \, \text{Nmm} \, \\
{{< /katex >}}
{{< katex display >}}
F_{alt} = \frac{32475 - (1155 \times 5 + 2300)}{2} = 12200 \, \text{N} \\
{{< /katex >}}
{{< katex display >}}
F_{med} = \frac{32475 + (1155 \times 5 + 2300)}{2} = 20275 \, \text{N}
{{< /katex >}}

Using a similar methodology as employed in the static design calculation but incorporating alternating and mean components, it results:

{{< katex display >}}
\tau_{M_{\text{alt}}} = \frac{M_{\text{alt}} \times 0.13}{I} + \frac{T_{\text{alt}} \times \frac{0.5964}{2}}{J} = \frac{51902.5 \times 0.13}{\frac{0.0335}{\sqrt{2}} \times 0.01} + \frac{1028.2 \times \frac{0.5964}{2}}{\frac{0.0418}{\sqrt{2}} \times 0.01}
{{< /katex >}}

{{< katex display >}}
\tau_{M_{\text{med}}} = \frac{M_{\text{med}} \times 0.13}{I} + \frac{T_{\text{med}} \times \frac{0.5964}{2}}{J} = \frac{79872.5 \times 0.13}{\frac{0.0335}{\sqrt{2}} \times 0.01} + \frac{1221.8 \times \frac{0.5964}{2}}{\frac{0.0418}{\sqrt{2}} \times 0.01}
{{< /katex >}}

For the calculation of direct shear stress:

{{< katex display >}}
\tau_{V_{alt}} = \frac{F_{\text{alt}}}{A_s} = \frac{12200}{0.0172} = 7.092 \times 10^5 \, \text{Pa}
{{< /katex >}}
{{< katex display >}}
\tau_{V_{med}} = \frac{F_{\text{med}}}{A_s} = \frac{20275}{0.0172} = 1.179 \times 10^6 \, \text{Pa}
{{< /katex >}}

For calculating the total stress due to moments and shear force:

{{< katex display >}}
\tau_{\text{alt}} = 1.5 \sqrt{\tau_{M_{alt}}^2 + \tau_{V_{alt}}^2} = 4.43 \times 10^7 \, \text{Pa}
{{< /katex >}}
{{< katex display >}}
\tau_{\text{med}} = 1.5 \sqrt{\tau_{M_{med}}^2 + \tau_{V_{med}}^2} = 6.77 \times 10^7 \, \text{Pa}
{{< /katex >}}

The minimum-to-maximum stress ratio was obtained using the following equation:

{{< katex display >}}
R = \frac{\tau_{\text{Mmed}} - \tau_{\text{Malt}}}{\tau_{\text{Mmed}} + \tau_{\text{Malt}}} = 0.208
{{< /katex >}}


The minimum stress is never zero because there will always be a certain associated weight, either from the weight of the beam itself or from the load imposed by the winch car. To conclude, let's calculate the safety factor values according to the Goodman criterion and the yield criterion. For this, it is important to define the fatigue limit stress using the **Marin factors**.

Hence:

- Se' = 0.5 {{< katex >}}\times{{< /katex >}} 830 = 415 MPa
- {{< katex >}}Ka = a \times S_{ut}^{-b} = 0.702{{< /katex >}} (hot rolled)
- Kb = 1 
- Kc = 0.59 (considering shear)
- Kd = 1 (operating at room temperature)
- Ke = 1 (reliability at 50%)
- Kf = 1 (corrosion and other factors neglected)


Therefore, obtaining a fatigue limit stress of Se = 172 MPa.
{{< katex display >}}
n_{\text{Goodman}} = \frac{1}{\frac{\tau_{\text{med}}}{S_e} + \frac{\tau_{\text{alt}}}{\frac{2}{3}S_{ut}}} = \frac{1}{\frac{67.7}{172} + \frac{44.3}{\frac{2}{3} 830}} = 2.111
{{< /katex >}}

{{< katex display >}}
n_{\text{Yield}} = \frac{\frac{1}{\sqrt{3}} \times S_y}{\tau_{\text{alt}} + \tau_{\text{med}}} = 2.37

{{< /katex >}}


### 2.6. Bolted Joint

![12aaa](https://live.staticflickr.com/65535/53357767665_b445ddd2ec_c.jpg)

The study will proceed with the bolted joint in terms of static and yielding analysis. The components to be bolted are two AISI 1095 HR steel plates, each with a thickness of 10mm. A total of 6 bolts are accounted for.

#### 2.6.1 Bolt Load


To calculate the tensile force in the bolts furthest away, P can be determined as follows:

{{< katex display >}}
P = \frac{M \times s}{\sum s_i} = \frac{131775 \times 191 \times 10^{-3}}{0.117366} = 214449 N
{{< /katex >}}


{{< hint important >}}
s is the distance from the farthest bolt to the hinge line. 
{{< /hint >}}

In this case, there won't be any shear force on the bolts since there's no torsion in the plane perpendicular to the bolts.

#### 2.6.2 Bolt Selection

To select the bolts, an iterative process with rapid convergence was used. It began with an initial estimate of C (joint stiffness constant) equal to 0.35. Following this, the maximum preload force 𝐹𝑖 was calculated to determine the minimum stress area value.

{{< katex display >}}
F_b = \frac{C \times n_b \times P}{\frac{1}{\chi} - 1} = 399768 \, \text{N} \\
{{< /katex >}}

Given {{< katex >}}\chi{{< /katex >}} = 0.75 for non-permanent connections and {{< katex >}}n_b{{< /katex >}} = 2.

{{< katex display >}}
F_{\text{comb}} = n_{\text{comb}} \left( \frac{P_s}{f_m} + (1 - C)P \right) = 247475 \, \text{N} \\
{{< /katex >}}

In this case, there are no shear forces on the bolts, so Ps = 0.

{{< katex display >}}
F_i = \max{F_{\text{b}}, F_{\text{comB}}} = 399768 \, \text{N} \\
{{< /katex >}}

{{< katex display >}}

A_{t_{min}} = \frac{F_i}{\chi \times S_p} = 642 \, \text{mm}^2 \\
{{< /katex >}}

Since these bolts will be structural components, class 10.9 was chosen, corresponding to a proof load Sp = 830 MPa. With the obtained value of {{< katex >}}A_{t_{min}}{{< /katex >}}, resulting from table 8-1 [of Shigley's Mechanical Design book](https://books.google.at/books/about/Shigley_s_Mechanical_Engineering_Design.html?id=B7wivgAACAAJ&redir_esc=y), it is feasible to determine the nominal diameter that meets the minimum area requirement. In this case, at least an M36 bolt will be required.

| Bolt      | d (mm) | At (mm²) | Ar (mm²) | P (mm) | Sp (MPa) | Su (MPa) |
|----------|--------|------------|------------|--------|------------|------------|
| M36x4    | 36     | 817        | 759        | 4      | 830        | 1040       |



Next, C is recalculated, and the compliance with the safety factor is verified. The threaded length of the bolt can be calculated as follows:

{{< katex display >}}
LT = 2 \times d + 6 = 78 mm (L < 125mm) \\
{{< /katex >}}


Having the concern of having threads in only one of the two plates to avoid stress concentrations, the bolt length should be 90mm, resulting in lt = 90-78 = 12mm of unthreaded portion within the 20mm of engagement (two 10mm plates). Consequently, the threaded engagement length will be ld = 20-12 = 8 mm. The unthreaded area corresponds to:

{{< katex display >}}
A_d = \frac{\pi \times d^2}{4} = 1017.36 \, \text{mm}^2 \\
{{< /katex >}}


Thus, obtaining the following stiffness values:

{{< katex display >}}
k_b = \frac{A_d \times A_t \times E}{A_d \times l_t + A_t \times l_d} = 9.728 \times 10^9 \\
{{< /katex >}}
{{< katex display >}}
k_m = \frac{0.5744 \times p_i \times E \times d}{2 \times \left( \frac{5 \times (0.5774 \times l + 0.5 \times d)}{0.5774 \times l + 2.5 \times d}\right)} = 18.3 \times 10^9 \\
{{< /katex >}}

It is now possible to calculate the joint stiffness:

{{< katex display >}}
C = \frac{k_b}{k_b+k_m} = 0.347 \\
{{< /katex >}}

The proof force is given by {{< katex >}}F_p = S_p \times A_t{{< /katex >}} = 678110 N, and the preload force can now be obtained as {{< katex >}}F_i = F_p \times \chi{{< /katex >}} = 508582.5 N. The static and combined safety factors are determined through the following calculation:

{{< katex display >}}
n_{\text{est}} = \frac{F_p - F_i}{C \times P} = 2.54 \\
{{< /katex >}}
{{< katex display >}}
n_{\text{comb}} = \frac{F_i}{\frac{P_s}{f_m} + (1 - C) \times P} = 4.11 \\
{{< /katex >}}

The combined safety factor, in this case, is equivalent to the separation safety factor of the joint since  {{< katex >}}P_s{{< /katex >}} = 0. The slip safety factor is also not applicable for the same reason, as there is no slipping.

{{< hint note >}}
All safety factors are higher than the design safety factor. Thus, the bolts used in the joint are verified accordingly. ✔️
{{< /hint >}}

## 3 Considerations & Future Tasks

Undertaking a project like this can raise awareness of the complexities involved in designing and analyzing structural or mechanical equipment. Such an experience often encourages seeking information from standards and technical documentation, fostering an investigative approach to problem-solving throughout the project.

I consider it was a great opportunity to develop problem-solving skills and an analytical -- investigative mindset.

For future work, I would say there are several potential avenues to explore:

{{< hint example>}}
- **Refinement and Optimization**: Consider refining the design further for enhanced efficiency or cost-effectiveness. 

- **Failure Analysis**: Explore potential failure modes and conduct a failure analysis to anticipate weaknesses or stress points that might arise during operation. This could involve simulations or in-depth theoretical studies.

- **Material and Manufacturing Improvements**: Investigate alternative materials or manufacturing techniques that might enhance the performance, durability, or ease of production of the designed equipment.

- **Real-World Implementation**: If possible, observe or participate in the implementation or assembly of the designed structure or equipment to gain practical insights and refine future designs based on real-world constraints. In this case, it was considered for industry used, but a generalization could also be implemented for other use cases.

- **Environmental and Sustainability Considerations**: Consider incorporating sustainability features into the design or assessing the environmental impact of the equipment. This could involve materials sourcing, energy efficiency, or end-of-life considerations.
{{< /hint>}}

