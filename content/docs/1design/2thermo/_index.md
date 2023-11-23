---
weight: 2
bookHidden: false
title: "ThermoCup"

---

# **ThermoCup**

## 1.1 What is the ThermoCup?

ThermoCup is a unique competition introduced initially at the Technical University of Lisbon during the MecanIST conferences in early 2016. Its primary objective is to challenge participants to conceive, design, and construct a steam boat. The competition covers a comprehensive spectrum of engineering aspects, incorporating various stages of the project lifecycle, from the initial conceptualization and design phase to the practical implementation, prototyping, and subsequent optimization processes.

![11e](https://live.staticflickr.com/65535/53336443281_ef63a9f75e.jpg)
![12e](https://live.staticflickr.com/65535/53336908180_08d5489b12_n.jpg)

## 1.2 Transversal Disciplines

The challenges within ThermoCup likely encompass a range of engineering disciplines, including:

- **Mechanical Engineering**: To design the physical structure of the steam boat, accounting for structural integrity and efficiency.

- **Thermal Engineering**: Understanding and optimizing the heat transfer mechanisms involved in generating steam to power the boat.

- **Fluid Dynamics**: Analyzing the flow of steam and water within the boat's components to maximize propulsion and efficiency.

- **Computational Modeling**: Employing Ansys Fluent workbench for virtual simulations to predict and optimize the boat's performance before physical construction.

## 1.3 Initial Considerations

In the context of designing a steam boat for ThermoCup, the significance of defining boundary conditions and material selection cannot be overstated. These initial steps essentially set the groundwork for the entire project, exerting a profound influence on the boat's performance and its eventual success in the competition.

- **Boundary Conditions**: Determining the environmental constraints, operational parameters, and physical limitations within which the steam boat will be mainly vital to perform **CFD** simulations. This includes factors such as the type of water body the boat will navigate, assumption of variations in temperature and pressure, and even limitations on the size and weight of the boat. In this case, it was intended to be an indoors competition - involving not so high velocities. Hence, *mostly* laminar flow, therefore a lot of simplifications were conducted to get a rough estimation of how each parameter would impact our results. However, accurately defining these boundary conditions allows participants to tailor their designs to suit real-world scenarios, ensuring the boat's functionality under specific circumstances.

- **Material Selection**: The choice of materials for constructing the steam boat significantly impacts its structural integrity, weight, buoyancy, and overall performance. For instance, selecting lightweight yet durable materials for the boat's structure can enhance its maneuverability and efficiency. Similarly, heat-resistant materials might be crucial for components exposed to high temperatures in the steam propulsion system.


{{< hint [info] >}}
 **But...**

Keep in mind that the competition's rules imposed caused certain constraints or guidelines regarding the materials that can be used, limitating creativity work to develop within those boundaries.
{{< /hint >}}


## 1.4 Computational Fluid Dynamics

The utilization of Computational Fluid Dynamics (CFD) simulations with Ansys Fluent workbench within the ThermoCup competition marked a significant and intriguing facet of the event. Ansys Fluent is a robust software suite widely employed in engineering disciplines to simulate and analyze fluid flow, heat transfer, and associated phenomena in intricate detail.

![simmm](https://8020engineering.com/wp-content/uploads/2018/10/8to15knots.gif)

In the context of ThermoCup, the incorporation of Ansys Fluent implied a multifaceted challenge for everyone beyond merely constructing a physical steam boat! It introduced a dimension where participants leverage advanced virtual simulations to iteratively refine their designs and optimize the performance of their boats via:

- Virtual Prototyping
- Performance Optimization
- Predictive Analysis

Surely using machine learning tools this could have been more efficient, but for that moment it yielded good results!


## 1.5 Our Solution

{{< details "Attempts 1" close >}}

![ss](https://live.staticflickr.com/65535/53336960300_e441411f17_w.jpg)

{{< /details >}}

{{< details "Attempts 2" close >}}

![14e](https://live.staticflickr.com/65535/53335570407_8c8504613f_c.jpg)

{{< /details >}}

{{< details "Attempts ALL" close >}}

![14e](https://live.staticflickr.com/65535/53335622417_63e481330f_w.jpg)

{{< /details >}}


After countless combinations of material and design, the choice of cork as the structural material for the steam boat in the ThermoCup competition seemed to have been a well-considered decision. Cork is known for its buoyant and lightweight properties (refer to previous Ch.2), and its higher density compared to materials like polystyrene likely provided the necessary weight and structural stability for the boat. This was especially important when simulating the boat's performance under the natural conditions of flow, which can be challenging at higher speeds due to introduced turbulence flow and higher hydrodynamic forces, including drag and lift that can lead to steer off course as it interacts with the surrounding water.



## 1.6 Outlook

![15e](https://live.staticflickr.com/65535/53336678438_b04bd3672c_c.jpg)

{{< katex display >}}
v_{\text{km/h}} = \frac{8}{18.9} \times 3.6 \approx 1.53\text{ km/h}
{{< /katex >}}

Securing 3rd place in the competition, covering an 8-meter water lane in just 18.9 seconds, while achieving a average speed of 1.53 km/h, is a notable result. While it is not perfect, it compromised diverse engineering problem-solving skills between meticulous material selection and thoughtful design considerations by the team. 
