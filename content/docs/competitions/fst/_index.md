---
weight: 1
bookHidden: false
title: "Formula Student Lisbon"
---

# **FST 010e** | Suspension

![sada](https://media2.giphy.com/media/KyGTYGI7aNYNY3Ryr8/giphy.gif)

{{< hint warning>}}
**Disclaimer**: Only partial information is divulged here - mainly for car transmission design, gear fabrication and lubrication. It follows a similar process to the one implemented at [*Car Transmission Design*](https://ricardochin.com/docs/1design/5om/), but for epicyclical gear trains.
{{< /hint>}}

## 1 Background

During Formula Student (FS) competitions, students are tasked with creating, testing, and refining a race car adhering to specific guidelines outlined by [Formula Student Germany (FSG)](https://www.formulastudent.de/) and [Formula Society of Automotive Engineers (FSAE)](https://www.fsaeonline.com/). The [Formula Student Team at Tecnico Lisboa (FST Lisboa)](https://www.fstlisboa.com/) has been actively involved in these competitions since 2001. In 2019, with a team comprising over 40 members, they embarked on dual projects for their 10th generation cars. One project involved crafting a new electric prototype, termed `FST10e`, while simultaneously enhancing the existing `FST09e` — previously in competition during the summer of 2019 — with autonomous capabilities, transforming it into `FST10d`. `FST09e` was the team's 5th electric prototype and secured a commendable 9th place overall in FSG 2019, a notably competitive event within the Formula Student season.

{{< hint important  >}}
Each FS competition comprises three static events focusing on design, manufacturing, cost, and business plans. Concurrently, the dynamic events assess the car's performance through four different challenges:

- Acceleration: a straight line with a length of 75 m;

- Skidpad: This involves navigating an 8-shaped track to evaluate the vehicle's lateral acceleration capabilities.

- Autocross: A single lap around a handling track approximately 1 km in length, focusing on overall handling and speed.

- Endurance/Trackdrive & Efficiency: This event comprises several laps around a closed circuit. Trackdrive is designated for Driverless cars, while Endurance is for other vehicle types.

{{< /hint>}}

Before 2019, most FS competitions accommodated three distinct car classes: combustion, electric, and driverless. Vehicles in the driverless class were required to autonomously complete all four dynamic events. However, in response to the burgeoning investment in autonomous driving within the automotive industry, the FSG organization made a significant decision. Starting from 2021, there would no longer be a separate driverless class. Instead, all participating vehicles must perform the acceleration event in driverless mode, and from 2022 onward, driverless mode is also mandatory for the skidpad. Teams unable to accomplish these driverless events risk losing substantial points, amplifying the drive to develop this technology further.

![test](https://tecnico.ulisboa.pt/files/2016/08/projecto-fst-novabase-alcanc-a-3--lugar-na-republica-checa.jpg)


## 2 Gear Manufacturing Process


The production of gears in the `FST-10e` transmission set begins by carefully selecting the most suitable steel for the purpose, as previously detailed. For cylindrical spur gears, the subsequent step involves cutting a cylindrical block with dimensions slightly exceeding the gear's outer measurements. This block then undergoes a sequence of machining procedures to approximate the final gear form, incorporating optimizations for rim and web thickness. This resulting intermediate stage, known as a gear blank, closely resembles the final gear shape but lacks the tooth structure.

![203](https://live.staticflickr.com/65535/53371718897_04c7ae5330.jpg)


To achieve the final gear shape, the gear blank undergoes several essential machining processes. Firstly, lathing is employed to attain the cylindrical shape with the precise diameter needed. Following this, broaching is utilized to carve out holes for mass optimization. Additionally, milling is employed to reduce the web thickness, contributing to the refinement of the gear blank. These combined machining procedures ensure the gear blank progresses closer to its intended final form with the required specifications. The gear itself can be obtained from this gear blank through various cutting and finishing processes:

![wqe3](https://live.staticflickr.com/65535/53372814833_c69749d504_z.jpg)


### 2.1. Gear Forming

In gear form cutting, the cutting edge of the cutting tool has a shape identical with the shape of the space between the gear teeth. It is then possible to disguise two different machining operations, milling and broaching can be employed to form cut gear teeth:


#### 2.1.1. Milling

This method, one of the earliest and most familiar for crafting gears, involves the use of a milling machine. It's versatile, capable of creating nearly all types of gears. Using a form cutter, the process entails the cutter moving along the gear tooth's length at the appropriate depth, cutting one tooth at a time. After cutting a tooth, the cutter retracts, the gear blank rotates, and the process repeats for each tooth until completion. However, milling is now primarily utilized for gears that don't demand high dimensional precision. More recent and precise technologies have surpassed milling, rendering it somewhat outdated for high-precision gear manufacturing.

#### 2.1.2. Broaching


Broaching is a machining technique employing a toothed tool, known as a broach, to eliminate material. There are two primary types: linear and rotary. Linear broaching, more prevalent, involves the broach moving linearly along the workpiece's surface to make the cut. Conversely, rotary broaching entails the broach rotating and pressing into the workpiece to create an axisymmetric shape. Rotary broaching is typically used in lathes or screw machines. In both methods, the cutting action occurs in a single pass of the broach, ensuring high efficiency in the machining process.

![33](https://live.staticflickr.com/65535/53372947214_0427d8d6c0_z.jpg)

Broaching stands out as an efficient method to create gear teeth, especially internal ones, boasting rapid production and delivering precise, finely finished surfaces. However, due to the expense of broaches and the necessity for specific broaches per gear size, this approach is primarily suited for high-quality production settings where accuracy is paramount and justifies the investment in specialized tooling.

![dadsw3](https://live.staticflickr.com/65535/53373083260_e283cf13d4_z.jpg)


### 2.2. Gear Hobbing

Gear hobbing is a continuous process where the cutting edges of a rotating tool, the hob, shape the tooth flanks of a moving workpiece. This method generates teeth progressively through a series of cuts using a helical cutting tool. All movements in hobbing are rotary, with both the hob and gear blank continuously rotating, akin to meshing gears, until all teeth are formed.

Its primary advantage lies in versatility: this process can craft various gear types like spur, helical, worm wheels, serrations, and splines. Additionally, it boasts high productivity due to its continuous nature, enabling efficient gear production.


{{< columns >}}


![13e](https://live.staticflickr.com/65535/53372947199_04aa7e81a2_w.jpg)
<--->

![14ki0](https://live.staticflickr.com/65535/53372947189_f326c2bbc8_w.jpg)
{{< /columns>}}


### 2.3. Gear Shaping

Gear shaping involves a generating technique where a cutter, designed akin to a gear with cutting edges, is employed. The tool rotates at a specific speed ratio concerning the gear blank, ensuring that each cutter tooth forms one tooth space. Unlike gear hobbing, this method is capable of producing cluster gears, internal gears, and racks, expanding the scope of gear manufacturing possibilities.

### 2.4. Wire EDM

A wire electrical discharge machine (EDM) is adept at cutting intricate two-dimensional patterns in steel plates, offering a viable method for producing prototypes and small batches of parts, like spur gears. 

{{< hint info>}}
**Mechanism:** a fine brass or tungsten wire, electrically charged, threads through a small hole in the steel plate. This wire moves through programmed patterns above and below the plate, cutting the steel by removing microscopic pieces of material to form the gear's teeth.
{{< /hint >}}

In this precise process, users program X and Y axis movements with micrometer-level resolution, controlling cutting speeds and power settings to suit specific applications, particularly for high-quality gears. However, the machine's movements are limited to straight lines or simple arcs, which poses a challenge for the complex involute gear tooth profile that doesn't consist of simple arcs. To overcome this, specialized software is crucial, accurately fitting arcs to the entire tooth shape—both involute and trochoid—to ensure a smooth and precise tooth surface. The arcs must seamlessly connect at their endpoints to facilitate smooth tool motion.

Precision in setting discharge intervals is crucial to achieving a smooth, low-roughness surface. Notably, the wire-EDM process maintains manufacturing precision as the final gear shape is formed in a single wire passage, eliminating concerns about minor misalignments in successive passes that might compromise tooth shape.

Overall, wire EDM presents high manufacturing precision, especially when managing intricate tooth profiles, and its single-pass method ensures minimal compromise on the final gear's tooth shape due to tool misalignments.

![123](https://live.staticflickr.com/65535/53372631991_ec54aac1c7.jpg)


In the `FST-10e`, the gear manufacturing process at `Lufimoldes` relied on wire EDM, a precise method chosen due to limited alternatives. Once the teeth shapes were obtained, attention turned to refining the teeth's surface roughness, a critical parameter. Grinding, a process aimed at reducing surface roughness, was adopted for this phase.

In the prior `FST-09e` prototype, initial gears were crafted via a method called shaving, which later presented operational challenges, particularly excessive wear. Consequently, a new set of gears was necessitated, prompting a shift in manufacturers and the adoption of wire EDM. These wire EDM-produced gears, enduring a full season of testing and competitions, remain in good condition today.

Although wire EDM was the only viable alternative after the shaving process became untenable, the team considers it a suitable method for gear production. Their familiarity with this method, including expectations regarding surface roughness, manufacturing parameters, and scheduling, owing to continuity with the same manufacturer from the second set of `FST-09e` gears, solidifies their confidence in its suitability.

![2313s](https://live.staticflickr.com/65535/53372814818_ddbfd4226e.jpg)

### 2.5. Grinding

Grinding is a precision process used to achieve fine finishes and accurate dimensions in components that have undergone prior manufacturing stages. While it can also thin very hard materials, this isn't the aim for the gears in the `FST-10e`.

Grinding methods vary; commonly, grindstones or handheld tools like angle grinders are used, but these aren't recommended for small gears as they might cause damage. Thus, alternative approaches are sought.

Wire EDM, known for its micro-material removal, theoretically offers a means to reduce surface roughness by precisely managing the intervals between electrical discharges. Despite the opportunity provided by Lufimoldes for wire EDM-based grinding, the team hesitated due to concerns that this process might inadvertently damage the gear surfaces, potentially complicating subsequent surface treatments. Their decision was influenced by the successful surface treatment applied to `FST-09e` gears, which were solely shaped by wire EDM and didn't undergo posterior grinding. This experience suggested that teeth grinding might not be crucial.

The team explored other avenues, including abrasive flow machining (AFM) or extrude honing. AFM involves the flow of an abrasive-laden fluid through a workpiece, effectively eroding raised surface features. This method adapts well to gears, as the fluid conforms precisely to their intricate and narrow surface intervals. The process optimally removes material from restricted flow areas, ensuring uniform finishing and enhancing gear meshing uniformity, reducing wear, and bolstering resilience against failure.

### 2.6. Gear Failures

Throughout their operational lifespan, gears endure substantial stresses and cyclic loads. These, coupled with various factors like elevated temperatures, high pressure, misalignment along the gear axis, or inadequate lubrication, can lead to several types of gear failures. The most common failure modes include:

#### 2.6.1. Bending Fatigue

Bending fatigue failure arises due to cyclic bending stress at the root of the gear tooth. Stress occurs from a load with a variable-level-arm moving along the tooth profile during meshing. The damage process unfolds in three stages: crack nucleation (the starting point of the crack), crack propagation, and eventual unstable fracture. The critical section, or the site where the crack begins, is often at the tooth-root fillet. Here, stresses, heightened by the notch effect (resulting from changes in the component's cross-section, such as drill holes or gear tooth variations), reach their peak, leading to uneven stress distribution and stress peaks.

Once initiated, cracks typically progress through the thickness of the tooth root, although their path may vary depending on the gear's shape and stiffness. For instance, gears with a lightweight, thin-rimmed body might experience cracking along the gear rim, emphasizing the importance of adhering to [KISSsoft](https://www.solidworks.com/partner-product/kisssoft-interface-solidworks) parameters like rim and web thickness.

The fracture surface typically exhibits two distinct areas: a fatigue-crack growth region and a final unstable fracture area. "Beach marks" may become evident, especially in cases of intermittent gear operation that frequently interrupts crack propagation. These marks might also appear on idler gears, particularly where both flanks of each tooth undergo alternating stress cycles, as seen in instances like the `FST-10e` transmission, susceptible to alternating stresses during regeneration processes.

![sdaads](https://live.staticflickr.com/65535/53385086757_1cf43a5981_b.jpg)

#### 2.6.2. Pitting

Pitting, also known as macropitting, occurs due to surface damage caused by cyclic contact stress transmitted through a lubrication film in or near the elastohydrodynamic regime. It's a prevalent cause of gear failure and affects various machine components exposed to heavy loads and rolling/sliding contact, such as antifriction bearings and cams.

The damage typically manifests in the region of negative sliding between the tooth root and pitch line, especially in the dedendum. When mating gears share the same material and heat treatment, pitting is usually first observed on the gear with fewer teeth because it undergoes a greater number of load cycles. In complex gear systems like the `FST-10e's` gear train, where different gears mesh with varying numbers of teeth, this effect intensifies.

Pitting initiates with the formation of subsurface or surface-breaking cracks, which then propagate under repeated contact loading. As a crack grows and reaches the tooth surface, a small volume of material separates, leaving behind a pit. Extensive pitting can alter the tooth profile, leading to vibrations and audible noise. This alteration in sound and vibrations was instrumental in the discovery of issues in the FST-09e`` transmission set during testing when the car produced unusual noise.

This type of failure can occur in both through-hardened and surface-hardened gears, although surface-hardened gears often exhibit what is commonly referred to as micropitting.

![3131](https://live.staticflickr.com/65535/53386005701_dfc8f303a4_h.jpg)

#### 2.6.3. Micropitting


Micropitting has emerged as a more prevalent issue due to the increased use of surface-hardened gears made from high-quality, cleaner steel. Additionally, the use of modern lubricants with sophisticated additive packages intended for extreme conditions may indirectly contribute to micropitting.

This phenomenon involves the formation of small craters on the tooth surface, typically below the pitch line in the region of negative sliding. Initially, micropits resemble macropits but are approximately ten times smaller upon first appearance.

These tiny craters originate from short cracks on the surface and gradually remove surface material, akin to abrasive wear. However, micropitting is primarily a result of rolling/sliding contact fatigue affecting the tooth surface and subsurface layer. This fatigue arises from repeated normal and tangential loads in a boundary or mixed-lubrication regime. The ratio of oil-film thickness to mean-surface roughness is considered a crucial predictor of this damage.

Micropits exhibit light-scattering properties, giving the affected area a frosted, light-gray appearance, hence the terms "frosting" or "gray straining." This damage alters the tooth profile, particularly in areas experiencing negative sliding, potentially elevating transmission error, dynamic loads, vibrations, and noise levels. Moreover, severe cases of micropitting can affect the entire tooth flank, impacting gear meshing.

Micropitting can also serve as a precursor to macropitting and may promote bending fatigue failures in tooth flanks, posing additional risks to gear integrity.

![23132](https://live.staticflickr.com/65535/53386191593_ad0c80f545_h.jpg)


#### 2.6.4. Wear

Wear is an ongoing process involving the gradual removal of material from the surfaces of mating gear teeth, whether or not abrasive particles are present in the oil. For instance, rough spots or hard asperities on gear flanks can contribute to material removal from the corresponding mating flanks. This process is particularly accelerated when the hardened layer of surface-hardened gears is worn away. Severe wear in spur gears often leads to pointed teeth and a reduced profile contact ratio. Continual wear at the tooth roots can weaken the gear until it eventually fails.

This wear phenomenon typically occurs under boundary or mixed lubrication conditions, where a substantial oil film is lacking to adequately separate tooth surfaces. 

{{< hint tip >}}
To mitigate wear, mild antiwear additives can be beneficial. These additives assist in protecting surfaces by forming adsorbed or reacted layers, especially under critical lubricating conditions.

{{< /hint >}}

Examining worn-out teeth can provide valuable insights into the gear meshing quality. Unusual patterns observed on worn teeth may indicate potential misalignment in transmission shafts or unwanted displacements and slack in the system. Such inspections help in diagnosing and rectifying issues to ensure proper gear operation.

## 3 Design

### 3.1. Gears Profile

Gears come in two main types: standard gears and profile-shifted gears. Standard gears follow a typical tooth profile, while profile-shifted gears involve modifications to this standard shape by shifting the profile relative to the norm. These modifications cater to specific operational needs or optimize gear performance under particular conditions. Each type offers unique advantages, with selection based on factors like load capacity, efficiency, noise reduction, and wear resistance.

![213](https://live.staticflickr.com/65535/53371777247_865b83e570.jpg)

{{< hint note>}}
In a non-corrected (basic) gear, the teeth height is denoted as 2.25 times the teeth modulus, represented by 'm'. In the illustration, 'A' signifies the addendum (equal to 'm'), and 'D' stands for the dedendum (1.25 times 'm').{{< /hint >}}

While the tooth profile of racks remains straight, the tooth profile of involute gears varies based on the number of teeth. The involute tooth profile is curvilinear but tends to resemble the straight profile of a rack when the number of teeth increases.

![1231](https://live.staticflickr.com/65535/53373005344_edab44eb2d_c.jpg)

When the number of teeth is increased, the tooth profile gets thicker at the tooth-root and
can generate more strength. As for the tooth profile of a 10-teeth gear, it is gouged at the
tooth-root and under-cutting occurs.

### 3.2. Abstract

The transmission system in the `FST 010e` prototype serves to transfer power from the electric motors to the wheels, aiming to boost torque while reducing rotational speed. Housed within the hub, it connects to both the hub and the upright.

This transmission setup comprises a stepped planetary gear train featuring three planet gears, with the input directed into the sun gear and the output from the planet carrier. The system is purposefully engineered to deliver a gear ratio of 15.21:1, emphasizing speed reduction and torque amplification.


{{< columns >}}


![13e21e1e](https://live.staticflickr.com/65535/53373005364_23b8acd41c_w.jpg)
<--->

![14kie21e0](https://live.staticflickr.com/65535/53372872943_6f998e30d5_w.jpg)
{{< /columns>}}

### 3.3. Modeling

#### 3.3.1. General Dimensions

#### 3.3.2. Individual Dimensions

#### 3.3.3. Material

#### 3.3.4. Rating



## 4 Lubrication

Gearboxes, by their nature, involve high-speed contact between metal components under significant loads. Lubrication becomes crucial to safeguard against wear, corrosion, and heat generated by friction in these systems. This holds true for the transmission set used in `FST-10e`.

Diverse types of lubricants operate on a shared principle: they establish a friction-reducing film between moving surfaces in contact. To discern the most effective lubrication methods, it's imperative to scrutinize and differentiate the factors influencing various types of lubricants and their application. Considerations encompass viscosity, additives, base oils, operating conditions like temperature and pressure, gear design, and material composition.

### 4.1. Influencing Factors

#### 4.1.1. Viscosity 


The viscosity of a fluid, such as oil, refers to its resistance to deformation at a specified rate. Essentially, it quantifies how a fluid resists the movement of immersed objects through it and the motion of layers with varying velocities within the fluid. Mathematically, viscosity ({{< katex>}}\eta{{< /katex>}}) can be expressed as the ratio between the shearing stress (F/A) and the velocity gradient in the fluid (considering variable velocity in the x-direction along the z-plane).


{{< katex display>}}
\eta = \frac{\frac{F}{A}}{\frac{dv_x}{dz}}
{{< /katex>}}

This relationship can be expressed through Newton's equation, stating that the resulting shear of a fluid is directly proportional to the applied force and inversely proportional to its viscosity.

{{< katex display>}}
\frac{F}{A} = \eta \frac{dv_x}{dz}
{{< /katex>}}

{{< hint info>}}
Viscosity is typically measured in units of Pascal seconds Pa.s in the SI system or Poise P (1 Pa.s = 10 P). 
{{< /hint >}}

However, while the calculations often refer to simple viscosity, there's another important property known as kinematic viscosity. This metric represents the ratio of a fluid's viscosity to its density. Comparing oils with varying densities becomes more accurate and useful through kinematic viscosity measurements, allowing for a reliable comparison between different oil types.


{{< katex display>}}
\mu = \frac{\eta}{\rho}
{{< /katex>}}

Kinematic viscosity is commonly measured in square meters per second [m²/s], although this unit is relatively large for practical use. The more frequently used unit is the square centimeter per second [cm²/s], also known as the "stoke" (St). To make it more manageable, oil viscosity is often measured in centistokes (cSt), which equals 1 mm²/s.

{{< hint important>}}
Viscosity, a crucial property influenced by various factors, including temperature, is essential for maintaining consistent lubrication properties within the upright's transmission set. This system operates across a range of temperatures, necessitating reliable lubrication performance across that spectrum.
{{< /hint>}}

Analyzing oil viscosity includes various methods, with one common approach being the [SAE (Society of Automotive Engineers)](https://www.sae.org/) notation. SAE numbers describe motor oil behavior under low and high temperature conditions, relevant to start-up and operation. The first number, followed by the letter "W" for winter, signifies the oil's behavior during start-up, while the second number characterizes its behavior at high temperatures after engine operation. Lower SAE numbers denote oils suited for lower temperatures, often with lower viscosity, while higher numbers signify thicker, more viscous oils. For instance, `10W-40` oil maintains a viscosity not exceeding 7000 [MPa.s] in a cold engine at -25°C and not less than 2.9 [MPa.s] in high-pressure engine regions near overheating (150°C). Additionally, comparing oils through their viscosity at 40ºC (in cSt) is a common practice, often present in commercial oil designations.

#### 4.1.2. Operating Conditions

### 4.2. Types of Lubrication

### 4.3. Modes of Lubrication

### 4.4. Types of Lubricants







