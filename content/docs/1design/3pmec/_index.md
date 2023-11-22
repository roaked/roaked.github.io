---
weight: 3
bookHidden: false
title: "Mechanical Design"

---

# **1 Double Girder Crane for Industrial Application**

## 1.1. Description and Context

I carried out within the scope of the Mechanical Design Course, part of my M.Sc journey. It aims to dimension and study fundamental structural components of a double girder suspension crane. Bridge cranes are frequently used equipment in the industry, especially in metallurgy, playing a pivotal role and being deployable both indoors and outdoors.
A bridge crane is a piece of equipment that, despite not being highly complex, encompasses all the elements that warrant study, allowing for the application of theoretical concepts applied to a real-world scenario.

## 1.2. Goals

This project aims to conduct the dimensioning and study of fundamental structural components. The sizing will be executed in accordance with applicable standards and demonstrated through analytical calculations and finite element models. In addition to the structural elements of the equipment, fundamental mechanical components of the system, including welded and bolted connections, will also be sized. Coupled with the dimensioning aspect is the economic factor, aiming to minimize costs while ensuring the safety of the entire structure

## 1.3. Function, Description, and Operation of the Mechanism
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


## 1.4. Design Specifications
### 1.4.1. Design Requirements
The following requirements were considered for this project:
- Nominal load of 8 tons;
- Span between wheels of 10 meters;
- Workshop environment;
- Operating temperatures between 0 and 40 degrees Celsius;
- 8 hours of operation per day;
- Maximum translation speed of 25 m/min.

### 1.4.2. Project Constraints

- The decision was made to design a bridge crane for an enclosed environment.

## 1.5. Standards, Codes, and Regulations

In the completion of this work, the FEM Standards (European Federation of Materials Handling), particularly Chapter II, and Eurocode 3 were utilized. The FEM standards address specific issues related to structures and handling equipment, including cases such as bridge cranes and gantry cranes.

## 1.6. Approach

A study was conducted on various existing construction solutions available in the market for this type of equipment. For the girders, a box-type section was chosen with a constant thickness of 10mm, made of AISI 1095 steel, and measuring 10m in length. These girders consist of 4 steel plates welded together with a 5mm thick welding bead. The rails are positioned on the girders, not aligned with the vertical plane passing through the girder's centroid, as this alignment would create a high bending moment on the upper plate of the girder. The connection between the girders and the trolley frame is established using 6 bolts M36x4 CR 10.9 and welding with a 10mm thick bead.

The chosen hoist trolley is the STAHL - SH 5020, which has a wheelbase of 1m and a mass of 755 kg. The following data pertains to the assembly composed of the two girders, two trolley frames, and the hoist trolley:

- Weight: 1229.3 kg
- Maximum width: 3.4 m
- Maximum length: 10.14 m

## 1.7. Manufacturing and Assembly Processes

The steel plates are obtained through a cold rolling process. Their surface roughness with an arithmetic mean deviation is 4.8 micrometers. The geometric tolerance concerning the thickness of these components is {{< katex >}}\pm{{< /katex >}}0.1 mm.

For the resistant girders:

- 1. Welding of the two web plates to the bottom flange.
- 2. Welding of the internal transverse reinforcements. The end reinforcements of the girder, which will function as covers, will be welded in a manner that the welding beads remain inside the girder.
- 3. Welding of the top flange.
- 4. Welding of the rail.
- 5. Welding of the lateral reinforcements.
- 6. Welding of the base fixing.

The welding for the first, third, and fourth steps will be conducted using the Submerged Arc Welding process. This method is advantageous due to the substantial length of the welding bead, allowing for automation of the equipment carrying out the operation. The welding for the remaining steps will be executed using TIG (Tungsten Inert Gas) welding. The required equipment will be operated by a skilled worker.

## 1.8. Costs


The price of AISI 1095 Q&T steel is approximately €1/kg. Considering the girders weigh around 346.6 kg, their cost will be approximately €346.6. The labor cost is around €9/m, and the analyzed structure totals 53.2m, amounting to approximately €478.8.

## 1.9. General Simplifications

Only the main components were designed, namely the girders. The trolley frames and the remaining support structure were not designed. The studied overhead crane was interior, simplifying the analysis, as exterior conditions would introduce wind forces. For the sizing, it was considered that the hoist trolley was positioned in the middle of the girders (longitudinally), as this situation causes the greatest deformations.

# 2 Calculations

## 2.1. Material

Due to the high stresses to which the box girder of the overhead crane is subjected, it's necessary to select a structural steel with considerable yield strength to ensure the component meets service conditions and to minimize stress concentration and fatigue issues. With this in mind, the following steel with a tempering and tempering treatment was chosen:

 {{< katex display >}}
\begin{tabular}{|c|c|c|c|c|}
\hline
 S_u  &  S_y  &  E  &  G  &  \nu  \\
\hline
1210 MPa & 772 MPa & 210 GPa & 81 GPa & 0.3 \\
\hline
\end{tabular}
 {{< /katex >}}