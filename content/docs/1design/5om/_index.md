---
weight: 5
bookHidden: false
title: "Manual Car Transmission Design"
---

# **Spinning Wonders: The Gear Train Blueprint**


![trans](https://i.makeagif.com/media/4-30-2016/iSEE9u.gif)


## 1 Normal Gear Train Composed of Cylindrical Gears

### 1.1. Functionality

A gearbox is used when different values of torque and rotational speed are desired from one shaft (input shaft) to another (output shaft). This is achieved through the combination of gears of different sizes. A gearbox can also reverse the direction of rotation, causing the output shaft to rotate in the opposite direction to the input shaft.

### 1.2. Description

This gear train consists of four shafts: the engine shaft, the output shaft, the counter shaft, and a dummy shaft. All gears are cylindrical with straight teeth and are constantly engaged. The study focuses solely on the gear train and not on its driving method, assuming the engine shaft to be driven by any type of motor.

The input shaft has only one gear, meshed with another gear connected to the counter shaft, transmitting motion to it. The counter shaft has a total of four gears: two corresponding to first and second speeds, and another for reverse gear.

The gears for first and second speeds are engaged with gears connected to the output shaft via bearings, allowing them to rotate independently of the output shaft. There are two engagement sleeves on the output shaft that rotate in synchronization with it.

First and second speeds are achieved by meshing one of the engagement sleeves with a component linked to one of these gears supported by bearings, causing the gears on the output shaft and the engagement sleeve to rotate together. By engaging the engagement sleeve, linking the counter shaft to the output shaft via the gear connected to the engagement sleeve, and the gear linked to the counter shaft, the output shaft is connected to the counter shaft.

The third speed is obtained by meshing one of the engagement sleeves with a component linked to the engine shaft, directly linking the engine shaft to the output shaft.

For engaging reverse gear, the engagement sleeve meshes with a component linked to a gear, similar to first and second speeds. To achieve this, one of the gears connected to the counter shaft meshes with a gear linked to the dummy shaft. This dummy shaft has another gear that meshes with the gear linked to the output shaft via bearings, and this gear is connected to the component with which the engagement sleeve meshes.

The presence of the dummy shaft causes the direction of rotation in reverse gear to be opposite to that of the engine shaft.

### 1.3. Mechanism

In this design of a compound normal gear train - a gearbox with 4 gears including reverse, the rotational speed of the output shaft depends on a multiplication of transmission from various sets of gears. Among the components that make up the transmission is the synchronizer, whose function is to synchronize the rotations of gear pairs to allow precise engagement of the desired gear when in motion. 

{{< hint note >}}
In this project, synchronizers were not designed, so shifting between the various gears will have to be done with the gearbox elements stationary.
{{< /hint >}}

![1](https://live.staticflickr.com/65535/53356992646_48c10f3608_c.jpg)
![1a](https://live.staticflickr.com/65535/53357215953_dceca19cf4_h.jpg)


#### 1.3.1. Neutral Gear

![2](https://live.staticflickr.com/65535/53357326799_ce4d1e091a_z.jpg)

In this case, none of the gear sleeves is connected to a gearwheel, therefore, there is no power transmission from the countershaft to the output shaft.

![3](https://live.staticflickr.com/65535/53357326769_abb4d43b32_c.jpg)

#### 1.3.2. First Gear

![4](https://live.staticflickr.com/65535/53357215928_d2ce7a7931_z.jpg)

In this situation, one of the gear sleeves is connected to gearwheel 9, linking the countershaft to the output shaft and enabling power transmission to the output shaft. In this scenario, the transmission ratio between the motor shaft and the output shaft is 3.333.


![5](https://live.staticflickr.com/65535/53357449755_df71ffaba4_c.jpg)

#### 1.3.3. Second Gear

![6](https://live.staticflickr.com/65535/53357326744_97bf764632_z.jpg)

In this case, one of the gear sleeves is connected to gearwheel 8, linking the countershaft to the output shaft and enabling power transmission to the output shaft. In this situation, the transmission ratio between the motor shaft and the output shaft is 2.4.

![7](https://live.staticflickr.com/65535/53356992571_44d8d24c15_b.jpg)

#### 1.3.3. Third Gear

![8](https://live.staticflickr.com/65535/53357449670_815c9d3416_z.jpg)

In this situation, one of the gear sleeves directly connects the motor shaft to the output shaft, enabling power transmission to the output shaft. In this case, the transmission ratio between the motor shaft and the output shaft is 1.

![9](https://live.staticflickr.com/65535/53356120747_e74a264f60_c.jpg)

#### 1.3.4. Reverse Gear

![10](https://live.staticflickr.com/65535/53356992481_e629ca2efb_z.jpg)

In this case, one of the gear sleeves is connected to gearwheel 10, linking the countershaft to the output shaft, enabling power transmission to the output shaft. In this situation, the rotation of the output shaft is in the opposite direction to that of the motor shaft. The transmission ratio between the motor shaft and the output shaft is -3.333.

![11](https://live.staticflickr.com/65535/53356120707_5b932b0a08_c.jpg)


## 2 Available Alternatives Analysis

For the transmission to be designed, a comparison among the various gearbox options available in the market is necessary. There are three possible solutions:

- **Sliding-gear transmission**: In this type, the gear wheels of the output shaft mesh with those of the countershaft as per the speed requirements. Both the output shaft and the countershaft gear wheels are mobile. This is the oldest method.

- **Fixed-gear transmission**: All gear wheels are engaged based on transmission ratios. The gear wheels of the output shaft are supported by bearings and engagement is achieved through gear sleeves.

- **Epicyclic (planetary) gear transmission**: In this type of gearbox, control over the two input speeds is managed by the sun gear and the ring gear. For consecutive gear sets, there is a connection between the planet carrier of one set and the ring gear of the other. The speeds of the ring gears are controlled at a transmission control station. This method is utilized in automatic transmissions in automobiles.

These three options offer different mechanisms for achieving gear engagement and transmission control, each with its own advantages and applications. The choice among them would depend on the specific requirements and functionalities needed for the project at hand.

![12](https://live.staticflickr.com/65535/53357449610_bec32debbf_c.jpg)


{{< hint example>}}
See below for the comparison between the sliding-gear, fixed-gear and epicyclic-gear.
{{< /hint >}}

| Aspect                    | Sliding-gear|Fixed-gear   |Epicyclic-gear |
|---------------------------|-------------|-------------|-------------|
| Epicycloidal Fixed Gears   | Bad        | Good          | Good          |
| Speed of Changes           | Bad        | Neutral | Good      |
| Compactness                | Bad           | Good       | Good          |
| Wear Damage                | Bad            |Good       | Good          |
| Possibility of Helical Teeth | Bad        | Good       | Good           |
| Smoothness of Changes      |   Bad          | Neutral | Good      |
| Complexity Level           | Very good     | Good      | Neutral |
| Repair Ease                | Good         | Good       | Neutral |
| Robustness                 | Neutral      | Good       | Good         |
| Cost                       | Good         | Good         | Neutral |

A decision to opt for a fixed-gear transmission over an epicyclic gearbox based on the provided table seems well-reasoned. The simplicity of fixed-gear transmissions compared to the complexity of epicyclic gearboxes usually results in lower costs. Epicyclic gearboxes, being more intricate, generally lead to increased expenses due to the need for a longer project study period, assembly time, and the creation of a transmission control station. The epicyclic gearbox would require an electro-mechanical control station, whereas the others would be operated solely mechanically.

Both fixed and sliding-gear transmissions would likely have similar design and assembly costs since their components are more common and less intricate compared to the epicyclic gearbox.

Ultimately, the decision to choose a fixed-gear transmission aligns with the design's objectives of cost efficiency and simpler implementation, considering the complexities and associated costs of the alternatives.


## 3 Design Calculations

Let's proceed considering the requirements provided. To determine the gear parameters based on the specified transmission ratios, it is possible to continue calculating the gear teeth for each gear ratio.

- First gear: Reduction of 3.333
- Second gear: Reduction of 2.4
- Third gear: Ratio of 1
- Reverse gear: Reduction of -3.333 (rotation in the opposite direction)

These values were extracted [from a deep dive into manual transmission design forum](https://www.upownersclub.co.uk/threads/gear-box-ratios.3535/).

- Center distance between shafts: 70 mm
- Pitch diameter of the first gear: 60 mm
- Standard module (M): 2 
- Real pressure angle: α = 20º
- All gears are external and in contact with each other are lubricated with light mineral oil

### 3.1. Characteristics of the Gear Wheel


To calculate the number of teeth (Z1) of the first gear, the formula for the gear ratio of a pair of gears can be used:

{{< katex display >}}
\text{Gear ratio} = \frac{Z_1}{Z_2} = \frac{N_1}{N_2} = \frac{D1}{M} = \frac{60}{2} = 30
{{< /katex >}}

- Crown diameter ({{< katex >}}D_a{{< /katex >}}) for the first gear:

{{< katex display >}}
D_{a1} = D1 + 2M = 60 + 2 \times 2 = 64 mm
{{< /katex >}}

- Root diameter ({{< katex >}}D_f{{< /katex >}}) for the first gear:

{{< katex display >}}
D_{f1} = D1 - 2.5M = 60 - 2.5 \times 2 = 55 mm
{{< /katex >}}

- Base diameter ({{< katex >}}d_b{{< /katex >}}) for the first gear:

{{< katex display >}}
d_{b1} = D1 \times cos(\alpha) = 60 \times cos(20º) \approx 56.3816 mm
{{< /katex >}}

- Angular pitch ({{< katex >}}p_{ang}{{< /katex >}}) for the first gear:

{{< katex display >}}
p_{ang1} = \frac{2 \times \pi}{z_1} = \frac{2 \times \pi}{30} approx 0.2094º
{{< /katex >}}

### 3.2. Characteristics of the Gear Tooth

- Tooth height (h) for the first gear:

{{< katex display >}}
h_{1} = h_{a1} + h_{f1} = 2.25 \times M = 2.25 \times 2 = 4.5 mm
{{< /katex >}}

- Addendum ({{< katex >}}h_a{{< /katex >}}) for the first gear:

{{< katex display >}}
h_{a1} = M = 2 mm
{{< /katex >}}

- Deddendum ({{< katex >}}h_f{{< /katex >}}) for the first gear:

{{< katex display >}}
h_{f1} = 1.25 \times M = 2.5 mm
{{< /katex >}}

- Root clearance ({{< katex >}}j{{< /katex >}}) for the first gear:

{{< katex display >}}
j_1 = h_{f1} - h_{a1} = 0.25 \times M = 0.25 \times 2 = 0.5 mm
{{< /katex >}}

- Circular pitch ({{< katex >}}p{{< /katex >}}) for the first gear:

{{< katex display >}}
p_1 = \pi \times M = \pi \times 2 = 6.283 mm
{{< /katex >}}

- Tooth thickness ({{< katex >}}s{{< /katex >}}) and tooth space ({{< katex >}}e{{< /katex >}}) for the first gear:

{{< katex display >}}
s_1 = e_1 = 0.5 \times p_1 = 0.5 \times 6.283 = 3.142 mm
{{< /katex >}}

- Tooth gap ({{< katex >}}g{{< /katex >}}) for the first gear:

{{< katex display >}}
g_1 = p_1 - 2 \times s_1 = 6.283 - 2 \times 3.142 = 0 mm
{{< /katex >}}

- Diameter pitch ({{< katex >}}Pd{{< /katex >}}) for the first gear:

{{< katex display >}}
Pd_1 = \frac{1}{M} = \frac{1}{2} = 0.5 mm^{-1}
{{< /katex >}}

It's possible to calculate the parameters of the other gears using similar formulas to those applied previously for the first gear. Based on the given transmission ratios and specifications regarding the tooth profile, it's feasible to calculate characteristics of the other gears, such as number of teeth, diameters, tooth height, angular pitch, among others. The next figure represents a diagram of the gear assembly.

![14](https://live.staticflickr.com/65535/53357449590_dd52559536_z.jpg)

### 3.3. Gear Train Parameters

Thus:

| Gear      | E1    | E2    | E3=E5=E6=E7  | E4    | E8=E10 | E9    |
|-----------|-------|-------|--------------|-------|--------|-------|
| Z         | 30    | 40    | 20           | 25    | 50     | 45    |
| D (mm)    | 60    | 80    | 40           | 50    | 100    | 90    |
| Da (mm)   | 64    | 84    | 44           | 54    | 104    | 94    |
| Df (mm)   | 55    | 75    | 35           | 45    | 95     | 85    |
| db (mm)   | 56.382| 75.175| 37.578       | 46.985| 93.970 | 84.572|
| pang (º)  | 0.2094| 0.1571| 0.3142       | 0.2513| 0.1257 | 0.1396|
| h (mm)    | 4.5   | 4.5   | 4.5          | 4.5   | 4.5    | 4.5   |
| ha (mm)   | 2     | 2     | 2            | 2     | 2      | 2     |
| hf (mm)   | 2.5   | 2.5   | 2.5          | 2.5   | 2.5    | 2.5   |
| j (mm)    | 0.5   | 0.5   | 0.5          | 0.5   | 0.5    | 0.5   |
| p (mm)    | 6.283 | 6.283 | 6.283        | 6.283 | 6.283  | 6.283 |
| g (mm)    | 0     | 0     | 0            | 0     | 0      | 0     |
| s=e (mm)  | 3.142 | 3.142 | 3.142        | 3.142 | 3.142  | 3.142 |
| Pd (1mm)   | 0.5   | 0.5   | 0.5          | 0.5   | 0.5    | 0.5   |


#### 3.3.1. Extra Calculations

- Center distance between shafts:

{{< katex display >}}
a_{12} = \frac{D1 + D2}{2} = \frac{60 + 80}{2} = 70 mm
{{< /katex >}}

{{< katex display >}}
a_{38} = \frac{D3 + D8}{2} = \frac{40 + 100}{2} = 70 mm
{{< /katex >}}

{{< katex display >}}
a_{49} = \frac{D4 + D9}{2} = \frac{59 + 90}{2} = 70 mm
{{< /katex >}}

{{< katex display >}}
a_{57} = \frac{D5 + D7}{2} = \frac{40 + 40}{2} = 40 mm
{{< /katex >}}

{{< katex display >}}
a_{7,10} = \frac{D7 + D10}{2} = \frac{40 + 100}{2} = 70 mm
{{< /katex >}}


#### 3.3.2. First Gear


Representative diagram of the gear assembly for the 1st gear ratio:

![15](https://live.staticflickr.com/65535/53357326654_5fc3cfc716.jpg)


- Intermediate transmission ratio: {{< katex >}}i_{12}{{< /katex >}} = -1.333
{{< katex display >}}
i_{12} = - \frac{D_2}{D_1} = - \frac{80}{60} = -1.3333 
{{< /katex >}}

- Intermediate transmission ratio: {{< katex >}}i_{38}{{< /katex >}} = -2.5
{{< katex display >}}
i_{38} = - \frac{D_8}{D_3} = - \frac{100}{40} = -2.50
{{< /katex >}}

- Transmission ratio in the 1st gear: {{< katex >}}i_{18}{{< /katex >}} = 3.3333
{{< katex display >}}
i_{18} = i_{12} \times i_{38} \Rightarrow i_{18} = \frac{n_1}{n_8} = 3.3333 
{{< /katex >}}
{{< katex display >}}
n_8 = n_{\text{output}} = \frac{n_{\text{input}}}{i_{18}} = \frac{n_{\text{input}}}{3.3333} 
{{< /katex >}}

The rotational speed for the output shaft in the 1st gear is 3.(3) times lower than the rotational speed of the motor shaft.

#### 3.3.3. Second Gear


Representative diagram of the gear assembly for the 2nd gear ratio:

![16](https://live.staticflickr.com/65535/53357449530_3086fed306.jpg)


- Intermediate transmission ratio: {{< katex >}}i_{12}{{< /katex >}} = -1.333
{{< katex display >}}
i_{12} = - \frac{D_2}{D_1} = - \frac{80}{60} = -1.3333 
{{< /katex >}}

- Intermediate transmission ratio: {{< katex >}}i_{49}{{< /katex >}} = -1.8
{{< katex display >}}
i_{38} = - \frac{D_9}{D_3} = - \frac{90}{50} = -1.80
{{< /katex >}}

- Transmission ratio in the 2nd gear: {{< katex >}}i_{19}{{< /katex >}} = 2.4
{{< katex display >}}
i_{19} = i_{12} \times i_{49} \Rightarrow i_{19} = \frac{n_1}{n_9} = 2.4
{{< /katex >}}
{{< katex display >}}
n_9 = n_{\text{output}} = \frac{n_{\text{input}}}{i_{19}} = \frac{n_{\text{input}}}{2.4} 
{{< /katex >}}

As expected, the rotational speed of the output shaft in the 2nd gear will be higher than the speed of the output shaft in the 1st gear - an increase in the number of rotations, yet still 2.4 times lower than the rotational speed of the motor shaft.

#### 3.3.3. Third Gear

Since the defined transmission ratio is 1, as both shafts are connected by the gear sleeve, it can be concluded that the input shaft speed will be equal to the output shaft speed.

{{< katex display >}}
n_{\text{input}} = n_{\text{output}}
{{< /katex >}}

### 3.3.3. Reverse Gear

Representative diagram of the gear assembly for the reverse gear ratio:

![17](https://live.staticflickr.com/65535/53357215733_1f5b4ddc37.jpg)

For the gear associated with the reverse gear, it's important to consider the transmission provided by the countershaft that assists in reversing the direction. The transmission can be calculated as follows


- Intermediate transmission ratio: {{< katex >}}i_{12}{{< /katex >}} = -1.333
{{< katex display >}}
i_{12} = - \frac{D_2}{D_1} = - \frac{80}{60} = -1.3333 
{{< /katex >}}

- Intermediate transmission ratio: {{< katex >}}i_{56}{{< /katex >}} = -1
{{< katex display >}}
i_{56} = - \frac{D_6}{D_5} = - \frac{40}{40} = 1
{{< /katex >}}

- Intermediate transmission ratio: {{< katex >}}i_{49}{{< /katex >}} = -1.8
{{< katex display >}}
i_{7,10} = - \frac{D_10}{D_7} = - \frac{100}{40} = -2.5
{{< /katex >}}

- Transmission ratio in the 2nd gear: {{< katex >}}i_{19}{{< /katex >}} = 2.4
{{< katex display >}}
i_{1,10} = i_{12} \times i_{56} \times i_{7,10} \Rightarrow i_{1,10} = \frac{n_1}{n_10} = -3.3333
{{< /katex >}}
{{< katex display >}}
n_10 = n_{\text{output}} = \frac{n_{\text{input}}}{i_{1,10}} = - \frac{n_{\text{input}}}{3.3333} 
{{< /katex >}}

The output shaft rotates at a speed 3.(3) times slower than the motor shaft, mirroring the observation in the 1st gear. The negative sign signifies a reversal in the output shaft's rotation direction compared to the motor shaft.

### 3.4. Gear Relations

To check the performance of the gears and investigate the presence of potential external interferences, it's necessary to perform calculations based on their contact relations. For the meshing between gears E1-E2, the gear of the motor shaft with the first gear of the counter-shaft is computed as follows:

- Approach length ({{< katex >}}l_a{{< /katex >}})

{{< katex display >}}
l_a = \sqrt{R_{a2}^2 - (R^2 \cdot \cos(\alpha))^2} - R_2 \cdot \sin(\alpha) = 5.0586 \text{mm} 
{{< /katex >}}
{{< katex display >}}
\Rightarrow l_a = \sqrt{42^2 - (40 \cdot \cos(20^\circ))^2} - 40 \cdot \sin(20^\circ) = 5.0586 \text{mm}
{{< /katex >}}

{{< hint note >}}
R2 and Ra2 correspond to the pitch radius and crown radius of the wheel, while R1 and Ra1 correspond to the pitch radius and crown radius of the Pinion. For the meshing between gears E1-E2; Ra2 = (Da2)/2 = 42 and Ra1 = (Da1)/2 = 32.
{{< /hint >}}

- Retraction length ({{< katex >}}l_f{{< /katex >}})

{{< katex display >}}
l_f = \sqrt{R_{a_1}^2 - (R_1 \cdot \cos(\alpha))^2} - R_1 \cdot \sin(\alpha) = 4.8814 \text{mm}
{{< /katex >}}
{{< katex display >}}
\Rightarrow l_f = \sqrt{32^2 - (30 \cdot \cos(20^\circ))^2 - 30 \cdot \sin(20^\circ)} = 4.8814 \text{mm}
{{< /katex >}}

Knowing the values of the approach and retraction lengths, the conduction ratio  ({{< katex >}}\epsilon{{< /katex >}}) is given by:

{{< katex display >}}
\epsilon = \frac{l}{p \cdot cos(\alpha)}= \frac{l_a + l_f}{p \cdot \cos(\alpha)} = \frac{5.0586 + 4.8814}{6.283 \cdot \cos(20^\circ)} = 1.6836
{{< /katex >}}

The value of {{< katex >}}\epsilon{{< /katex >}} = 1.6836 {{< katex >}}\geq{{< /katex >}} 1.3, thus satisfying this condition. It is also feasible to assess the total slip related to the maximum specific slip:

{{< katex display >}}
g_{s1 \text{max}} = \frac{1}{i_{12}} \times \frac{D_2 \cdot \sin(\alpha) + 2 l_a}{D_1 \cdot \sin(\alpha) - 2 l_a} - 1 = \frac{1}{1.3333} \times \frac{80 \cdot \sin(20^\circ) + 2(5.0586)}{60 \cdot \sin(20^\circ) - 2(5.0586)} - 1 = 1.7017
{{< /katex >}}

{{< katex display >}}
g_{s2 \text{max}} = 1 - i \times \frac{D_1 \cdot \sin(\alpha) + 2 l_f}{D_2 \cdot \sin(\alpha) - 2 l_f} = 1 - 1.3333 \times \frac{60 \cdot \sin(20^\circ) + 2(4.8814)}{80 \cdot \sin(20^\circ) - 2(4.8814)} = -1.2944
{{< /katex >}}


{{< katex display >}}
g_{\text{total}} = \left(\frac{1}{R_1} + \frac{1}{R_2}\right) \frac{l_a^2 + l_f^2}{2 \cdot \cos(\alpha)} = \left(\frac{1}{30} + \frac{1}{40}\right) \frac{5.0586^2 + 4.8814^2}{2 \cdot \cos(20^\circ)} = 1.5338
{{< /katex >}}


For calculating efficiency, it's necessary to evaluate the coefficient of static friction {{< katex >}}\mu{{< /katex >}}. To do this, [I consulted the table in the engineering toolbox website](https://www.engineeringtoolbox.com/friction-coefficients-d_778.html). Considering the surface condition between the gears in a light mineral oil, we extracted the value of {{< katex >}}\mu{{< /katex >}} as 0.23 from the table.


Given that the friction factor is determined by f = tan({{< katex >}}\mu{{< /katex >}}), it is found that f = tan(0.23) = 0.234.

{{< katex display >}}
\eta = 1 - \frac{f}{\cos(\alpha)} \cdot \left(\frac{1}{D_1} + \frac{1}{D_2}\right) \frac{l_a^2 + l_f^2}{l_a + l_f} = 1 - \frac{0.1614}{\cos(20^\circ)} \cdot \left(\frac{1}{60} + \frac{1}{80}\right) \frac{5.0586^2 + 4.8814^2}{5.0586 + 4.8814} = 0.9169 = 91.69\%
{{< /katex >}}

{{< hint warning >}}
Efficiency is always a critical factor in cost reduction during operation. Using this formula, it becomes evident that it's not highly precise and heavily relies on the coefficient of static friction. For a more accurate result closer to reality, conducting some experimental tests would be necessary. However, it remains useful for estimating the influence of various variables on overall efficiency. 
{{< /hint >}}

To prevent the existence of primary external interferences, the minimum number of teeth on the pinion is calculated:

{{< katex display >}}
Z_{1 \text{min}} \geq -Z_2 + \sqrt{Z_2^2 + \frac{4 \cdot (1 + Z_2)}{\sin^2(\alpha)}} = -40 + \sqrt{40^2 + \frac{4 \cdot (1+40)}{sin^2(20^\circ)}} = 14.789 \approx 15 \text{ teeth}
{{< /katex >}}

Since in this first gearing E1-E2, Z1 = 30 teeth, the condition is verified, and there's no risk of primary external interferences. Similarly, the formulas can be applied to all gearings. A MATLAB code was developed to systematize these calculations.

![assm](https://live.staticflickr.com/65535/53356992391_03f6534a0f_w.jpg)


|      | E1-E2 | E3-E8 | E4-E9 | E5-E6 | E7-E10 |
|------|-------|-------|-------|-------|--------|
| Z1   | 30    | 20    | 25    | 20    | 20     |
| Z2   | 40    | 50    | 45    | 20    | 50     |
| i    | 1.3333| 2.5   | 1.8   | 1     | 2.5    |
| R1 (mm) | 30  | 20    | 25    | 20    | 20     |
| Ra1 (mm) | 32 | 22    | 27    | 22    | 22     |
| R2 (mm) | 40  | 50    | 45    | 20    | 50     |
| Ra2 (mm) | 42 | 52    | 47    | 22    | 52     |
| la (mm) | 5.059 | 5.180 | 5.125 | 4.596 | 5.180 |
| lf (mm) | 4.881 | 4.596 | 4.758 | 4.596 | 4.596 |
| ε    | 1.6836| 1.6558| 1.6738| 1.5569| 1.6558 |
| gs1max | 1.7017| 4.3677| 2.3267| 4.0955| 4.3677 |
| gs2max | -1.2944| -1.2864| -1.2529| -4.0955| -1.2864 |
| gtotal | 1.5338| 1.7862| 1.6189| 2.2479| 1.7862 |
| η (%)  | 91.69 | 90.16 | 91.17 | 86.82 | 90.16  |
| Z1min | 14.789| 15.145| 14.983| 13.438| 15.145 |


All {{< katex >}}\mu{{< /katex >}} values satisfy the condition of being {{< katex >}}\geq{{< /katex >}} 1.3. Furthermore, in all gearings, Z1 is greater than {{< katex >}}\Z_{1min}{{< /katex >}}, ensuring the absence of interferences.

### 3.5. Nominal Load

To comply with safety standards, a safety factor was defined using the Pugsley method.

![21](https://live.staticflickr.com/65535/53357326554_92d57763c6_w.jpg)

It was considered that material quality, repair, and inspection were not prioritized in this case. However, controlling the applied load and information regarding tests with similar parts was deemed important. The economic impact is also crucial, as one of the objectives is to minimize costs.

{{< katex display >}}
n_s = n_{sx} n_{sy} = 2.05 \cdot 1 = 2.05
{{< /katex >}}

With a safety factor of 2.05 obtained, in addition to using [the values from Shigley's Mechanical Design book](https://books.google.at/books/about/Shigley_s_Mechanical_Engineering_Design.html?id=B7wivgAACAAJ&redir_esc=y), the mechanical properties of the chosen material AISI 1040 QT at 650ºC can be extracted:

![129](https://live.staticflickr.com/65535/53357326564_ebda8b2ee2_c.jpg)

{{< katex display >}}
n_s = \frac{\sigma_y}{\sigma_{max}} \Rightarrow \sigma_{max} = \frac{434}{2} = 217 MPa
{{< /katex >}}

## 4 You Spin Me Right Round

The designed system met the initially set requirements. It's worth noting that all gearbox elements need to stop rotating before changing gears since it lacks synchronizers. It was opted for a gearbox without synchronizers to avoid complexity and considering its application. A potential use for such a gearbox, albeit larger and with more speeds, could be in a machine tool like a lathe. These machines can operate without changing the transmission ratio while running. If this type of gearbox were used, it might be in a low-cost machine tool, hence why cost was a priority in this project. An improvement to the system could involve adding synchronizers. This would allow the machine operator to change speeds without having to stop the machine.


## 5 Task for Later

Future work or potential improvements could include exploring:

- **Additive Manufacturing**: Investigating the feasibility of using additive manufacturing techniques to build components with enhanced precision or novel designs, potentially reducing manufacturing complexities.

- **Synchronizer Integration**: Developing and integrating synchronizers into the gearbox system to enable gear shifting without stopping the machine, enhancing its operational efficiency and convenience.

- **Material Selection and Enhancement**: Exploring other material options or enhancements that could improve the gearbox's performance, such as materials with higher heat resistance or increased durability.

- **Efficiency Optimization**: Conducting studies to optimize the gearbox's efficiency without compromising its functionality, potentially through design modifications or lubrication improvements.
