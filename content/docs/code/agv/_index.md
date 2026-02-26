---
weight: 1
title: "AGV: Stochastic Identification"
---

# **They See Me Rollin', They Navigatin'**

# System Identification: AGV

![raster](https://live.staticflickr.com/65535/53353039340_0f9b1f0747_w.jpg)


I wanted to understand how a cool robot called "Rasteirinho" works when told to perform certain tasks, and how it reacted to certain commands. To achieve this, real-time simulations were executed using Matlab and relevant toolboxes such as the [System Identification Toolbox](https://de.mathworks.com/help/ident/) and its reactions were studied. This helped understanding how the robot behaved when asked to do different things.


{{< hint info >}}
- 1. Deterministic Systems Analysis: The preliminary focus was on deterministic systems, where a consistent input produced a predictable output. This phase provided foundational insights into the system's response under controlled and repeatable conditions.

- 2. Transition to Stochastic Systems: The investigation then progressed to stochastic systems, encompassing both continuous and discrete models. Contrary to deterministic systems, stochastic processes introduced unpredictability. Notably, experiments employing filtered white noise as input demonstrated varying reactions across iterations, emphasizing the inherent stochastic nature of the system.

- 3. Data Collection and Analysis: Data collected from these experiments formed the basis for analysis, leveraging a spectrum of methodologies and tools available in Matlab. The diverse approaches facilitated a comprehensive examination of the system's behavior under stochastic conditions.{{< /hint >}}

The discussions and conclusions drawn stem from the outcomes obtained through multiple analytical methods. The variability observed in stochastic systems prompted insightful considerations regarding the "Rasteirinho" system's behaviour and characteristics. How this variability impacts the overall understanding and analysis of the system becomes a pivotal point for consideration and further exploration.

{{< hint tip >}}
Further investigation into the implications of stochastic behaviour on control mechanisms within the "Rasteirinho" system could deepen our understanding. Exploring advanced analytical techniques and expanding the scope of experiments might offer more nuanced insights into the system's behaviour.{{< /hint >}}

## 1 System Model

### 1.1. Rasteirinho 

The primary function of the "Rasteirinho" system is autonomous navigation along a predefined path delineated by the black line on the floor. Through the coordinated operation of its components, the system employs sensor data from the camera, interprets it using the path-following algorithm, and generates control signals to the motors for precise movement, ensuring the system stays aligned with the designated path. The "Rasteirinho" system comprises several interconnected components designed for path tracking. At its core, it features:

- Laptop with Camera: The system's sensory input is facilitated by a laptop equipped with a camera. This camera serves as the primary sensor to capture visual data from the environment.

- Base with Wheels: A base structure provides support for the components and houses wheels essential for locomotion.

- Motors and Encoders: The system incorporates motors to drive the wheels, enabling controlled movement along the designated path. Encoders are utilized to measure and track wheel rotations, aiding in precise movement control and feedback.

- Arduino Control: The Arduino platform serves as the control unit, orchestrating the interactions between the laptop, camera, motors, and encoders. It facilitates the integration of sensory information with control signals for effective navigation.

- Path Following Algorithm: The system is programmed with a path-following algorithm that interprets visual data captured by the camera to detect and follow the black line on the floor. This algorithm involves image processing techniques to analyze the captured images and determine the correct path for the system to follow.

![meso](https://live.staticflickr.com/65535/53353039335_46c3f34277_z.jpg)


The system's purpose is rooted in exploring concepts of robotics, control systems, and computer vision. By combining hardware (motors, encoders, etc.) and software (path-following algorithms, image processing), it aims to demonstrate autonomous navigation capabilities and the integration of various technologies for a specific task—following a marked path.


### 1.2. Non-linearities

The input signal provided to the system is derived from the deviation of the system from the center of the marked line on the floor. This deviation is likely quantified as a distance in pixels from the center of the line, considering the image captured by the camera, which has a resolution of 160 pixels width-wise. The assumed center is positioned at pixel 80.

The output signal corresponds to the distance from the system to a specific part of the line, presumably the portion furthest to the left in the captured image. This output signal's measurement allows the system to gauge its position relative to this reference point along the line.

{{< details "**Description:** Soft Non-linearities  - (click to expand)" close >}}
Variations in lighting conditions, camera placement, angle, imperfections on the lab ground, and camera resolution introduce soft non-linearities to the system. These factors influence the system's behaviour and response but were approximated in the model without significantly compromising its accuracy. Managing these non-linearities involved calibration and adjustment techniques to mitigate their effects on the system's performance.
{{< /details >}}


{{< details "**Description:** Hard Non-linearities  - (click to expand)" close >}}
The system encounters two hard non-linearities — namely, a dead zone and a saturation zone. These non-linearities stem from constraints related to the input voltage, resulting in minimum and maximum values. The dead zone represents a region where the input signal remains unresponsive or exhibits minimal response. On the other hand, the saturation zone indicates a limit where the system's response reaches its maximum capacity and remains constant despite further increases in the input signal.
{{< /details >}}

### 1.3. Sampling Time

The chosen sampling time, Ts = 1/30 seconds (or approximately 33.33 milliseconds), aligns with the frame rate of 30 frames per second supported by the system's camera.

This sampling frequency translates to a corresponding angular frequency of 190.4 radians per second (rad/s). This frequency is deemed adequate for the system's dynamics, as it accommodates the characteristics and behaviour of the system, which likely doesn't exhibit high-frequency responses.

By choosing an appropriate sampling time that aligns with the system's dynamics, the collected data can adequately represent the system's behaviour without introducing aliasing or missing high-frequency components that might not be relevant or present in the system's response.

## 2 Deterministic Identification

It is seen that in Simulink, the system received two distinct types of inputs: sinusoidal and square signals, each with different parameter settings. It is stated that by observing the resultant outputs corresponding to these inputs, multiple methods may be employed to derive the transfer function. The procedure, as conveyed, was visually represented through a diagram showcased within Simulink, outlining the step-by-step process involved in this analysis.

![m1312eso](https://live.staticflickr.com/65535/53352831253_644505363b.jpg)


### 2.1. Step Response Method

In the lab trials, data on input, output, and time were recorded. Various amplitudes of square waves were used to identify the step response with the least noise. The importance of obtaining high-quality data for analysis and system identification was emphasized, leading to the recording of multiple samples.

{{< details "**Sample 1:** Square Input with Amplitude = 25, Frequency 0.5 vs System Output - (click to expand)" close >}}
![m1312e241so](https://live.staticflickr.com/65535/53352831233_d8f6d8fdd0_b.jpg)
{{< /details >}}

{{< details "**Sample 2:** Square Input with Amplitude = 10, Frequency 0.5 vs System Output - (click to expand)" close >}}
![m1312e1241so](https://live.staticflickr.com/65535/53353058145_567da00851_b.jpg)
{{< /details >}}

{{< details "**Sample 2:** Square Input with Amplitude = 7.5, Frequency 0.3 vs System Output - (click to expand)" close >}}
![m13121e241so](https://live.staticflickr.com/65535/53353058160_6e6cfb8ddc_b.jpg)
{{< /details >}}


In the plots, disturbances at the start and end are observed, originating from the initial placement and stopping of the Rasteirinho. The presence of overshoot indicates that it's not a first-order system, as evident from the plots.
Interestingly, in a linear system, signal amplitude typically doesn't affect settling time, which contrasts with the observations, implying non-linearity. However, there's a linear characteristic where response gain increases with input amplitude, suggesting a semblance of linearity. Despite its non-linear nature, the system exhibits behaviors aligning with the superposition principle, enabling its modeling as linear. 


The [System Identification Toolbox](https://de.mathworks.com/help/ident/) in MATLAB was utilized to estimate the system's order and the count of zeros. This step is crucial for signal pre-processing, providing insights into the system's order for subsequent analysis.


![m13121e24251so](https://live.staticflickr.com/65535/53353058155_db396dff8a_z.jpg)

The upcoming equation describes the relationship between the input and output of your system, derived from the analysis performed using the [System Identification Toolbox](https://de.mathworks.com/help/ident/). It's a 4th-order model with 4 poles and 3 zeros.


{{< katex display >}}
G_{\text{ident}}(s) = \frac{9.375s^3 + 448.9s^2 + 3793s + 6750}{17.03s^4 + 17.03s^3 + 435.1s^2 + 3263s + 7500}
{{< /katex >}}


### 2.2. Deconvolution Method


The deconvolution method is being employed to derive the impulse response by deconvoluting the convolution integral. The input and output data from the step function are utilized to compute the impulse response, which is subsequently compared with the impulse response obtained from the transfer function using the [System Identification Toolbox](https://de.mathworks.com/help/ident/). 


![213](https://live.staticflickr.com/65535/53353070530_0cdb3fc4ed_z.jpg)


Notably, while similarities are evident, discrepancies exist in the responses due to several reasons. The deconvolution method proves highly sensitive to measurement noise, encountering challenges with an ill-conditioned, [unsymmetrical Toeplitz matrix](https://en.wikipedia.org/wiki/Toeplitz_matrix) 'u.' Moreover, the impulse response, derived as the derivative of the step response, tends to amplify noise.

Following this comparison, the ['ir2dtf' function](https://github.com/roaked/agv-system-identification/blob/main/ir2dtf.m) and ['d2c' MATLAB function](https://de.mathworks.com/help/ident/ref/dynamicsystem.d2c.html) are employed to derive [the step response of the system identified through deconvolution - code available](https://github.com/roaked/agv-system-identification/blob/main/Deconv.m). This process involves resampling the deconvolution data and carefully selecting the most representative points from the transient response. The choice of a 4th-order system proves optimal for achieving favourable outcomes. The resulting step response is subsequently obtained for further analysis.

![2212113](https://live.staticflickr.com/65535/53352842998_f87cea2d5a_c.jpg)

Yielding:

{{< katex display >}}
G_{\text{deterministicStep}}(s) = \frac{309.8s^3 + 7682s^2 + 4.842 \times 10^5s + 1.745 \times 10^6}{18.84s^4 + 801.3s^3 + 991.6s^2 + 1.868 \times 10^4s + 1.745 \times 10^6}
{{< /katex >}}


Subsequently, comparing the impulse responses of the different methods and assess their validity:

![22121122113](https://live.staticflickr.com/65535/53353070540_078ba452c4_c.jpg)

Despite the discrepancies evident in the graphs, a notable convergence emerges. Primarily, the significant deviation at the initial peak possibly arises from the amplified noise during the deconvolution process. However, upon a closer examination of the remaining parameters, notable similarities become apparent. The graphs demonstrate congruent peak times and settling times, signifying stability across all observations.


### 2.3. Levy Method

[The Levy Method, designed to minimize error between data and estimated model frequency responses, employs curve fitting in amplitude and phase within the numerical frequency domain - code available](https://github.com/roaked/agv-system-identification/blob/main/levy.m). This method's utilization of Bode plots enables a comprehensive comparison of results as follows:

![2212112212113](https://live.staticflickr.com/65535/53352621011_d693246b00_c.jpg)

Prior to applying the [Levy function](https://github.com/roaked/agv-system-identification/blob/main/levy.m), a crucial step involved computing the Fourier transform of the deconvolution. This was achieved using a bespoke function that employs the discrete Fourier transform algorithm. This function determined the gain, phase, and associated frequencies from the acquired points, serving as inputs for the subsequent Levy function.

Upon examination, it became apparent that higher frequency values within the Fourier Transform failed to accurately represent the system dynamics. Despite attempts to represent the initial 10 points, not all points aligned on the phase diagram due to disparities. In the magnitude plot, discrepancies were also notable for higher frequencies. These discrepancies stem from the Rasteirinho's slower system response, where noise interference significantly impacts higher frequencies. Consequently, the initial few points of the Fourier Transform were selectively used to generate the Bode plot via the Levy method.

Following the execution of the Levy function, the presence of unstable poles within the transfer function needed rectification. This requirement arose from occasional instability in the estimated function, a common occurrence within the numerical nature of the Levy method — an aspect encountered during analysis.

![2212421112212113](https://live.staticflickr.com/65535/53353070520_4f3d396b09_c.jpg)

The comparative plot above offers a glimpse into step responses acquired via distinct methodologies (Levy, Matlab Sim toolbox and decovolution), unveiling a remarkable resemblance in their behaviour. However, notable distinctions arise, particularly in the deconvolution method, which exhibits a pronounced overshoot peak. This discrepancy suggests a heightened susceptibility to noise interference within the deconvolution process. Consequently, the resulting transfer function obtained from these analyses is as follows:

{{< katex display >}}
G_{\text{LevyData1}}(s) = \frac{3085s^2 + 3.098 \times 10^5s + 5.176 \times 10^6}{s^3 + 123s^2 + 2864s + 6.932 \times 10^4}
{{< /katex>}}


![221242111221241113](https://live.staticflickr.com/65535/53352948109_a2f9d5ab54_c.jpg)

### 2.4. Data Analysis and Assessment

The prior findings stemmed from the analysis of a singular transient response, selected from the numerous step inputs showcased previously. Considering multiple available plots and simulated square functions, it becomes imperative to validate the conclusions drawn from the chosen transient response against the entirety of the responses.

These initial results, derived from the initial experiment, will now undergo comparison with the other two initially collected data samples. This comparative analysis aims to ascertain the continued validity of our conclusions across the spectrum of responses gathered, ensuring the robustness and generalizability of our findings.


{{< katex display >}}
G_{\text{data2Deconv}}(s) = \frac{368.5s^3 + 412.6s^2 + 1.288 \times 10^5s + 4.188 \times 10^5}{s^4 + 23.09s^3 + 512.3s^2 + 6501s + 2.957 \times 10^4}
{{< /katex>}}


{{< katex display >}}
G_{\text{data3Deconv}}(s) = \frac{161.6s^3 + 2236 + 1.804 \times 10^5s + 1.508 \times 10^5}{s^4 + 14.59s^3 + 723s^2 + 5760s + 6.186 \times 10^4}

{{< /katex>}}

![221242111221215141113](https://live.staticflickr.com/65535/53352948114_c8450a4b36_z.jpg)

![21441](https://live.staticflickr.com/65535/53353016659_5628c76086_z.jpg)

The comparison conducted via the Step Method using deconvolution and estimation via the [System Identification Toolbox](https://de.mathworks.com/help/ident/) appears valid, despite a slight frequency offset observed in the step responses across different data samples. These discrepancies can be attributed to disturbances impacting the system dynamics, altering an array of variables in real-time. Consequently, achieving identical step responses from diverse data samples becomes entirely unattainable.

## 3 Stochastic Identification

When conducting stochastic system identification, the input signal is inherently random, making it challenging to describe the system using conventional physical laws. Analyzing such a system requires a distinct methodology, employing specialized functions like the correlation function, distribution function, and density function. This approach hinges on assuming stationarity within the system, ensuring that both the mean and variance remain constant over time. Moreover, it relies on the assumption of ergodicity, implying that a single sample encapsulates all the statistical properties of the system.

![123](https://live.staticflickr.com/65535/53351811542_f28b4b537a_c.jpg)

### 3.1. Data and Correlation Analysis 

It was recorded data for tests with different noise power. Using a noise power of 3 the following data was recorded:

![1242423](https://live.staticflickr.com/65535/53353140190_1e84af4460_c.jpg)



Following the application of the ["detrend" function in MATLAB](https://de.mathworks.com/help/matlab/ref/detrend.html) to eliminate any offset, the subsequent use of the ["cra" MATLAB function](https://de.mathworks.com/help/ident/ref/cra.html) provided essential outcomes. This function facilitated the derivation of the covariance function, capturing the relationship between the input and output signals, along with the correlation from input to output. Additionally, it aided in estimating the impulse response, crucial for understanding system behavior and dynamics.

![999](https://live.staticflickr.com/65535/53352690106_18d1772664_c.jpg)


As anticipated, the correlation from input to output tends toward zero for negative lags, suggesting a unidirectional correlation wherein the output is related to the input, but not vice versa. Next, it's valuable to compare the impulse response obtained from the stochastic system identification with the deterministic one derived from deconvolution. This comparison allows to discern and evaluate potential differences between the responses generated by these distinct methodologies.

![38418](https://live.staticflickr.com/65535/53353016639_ce2c9e35ca_c.jpg)

The functions depicted in the plot exhibit a noticeable time delay, suggesting that the components within the system might respond after the initial occurrence of the disturbance. Apart from this delay, the graphs display similarities, aligning with initial expectations. This similarity indicates consistency between the responses generated, validating the anticipated behaviour of the system under analysis.


### 3.2. Step Response 

Now, following a procedure akin to the one employed in deterministic processes, it is possible to utilize the ["ir2dtf"](https://github.com/roaked/agv-system-identification/blob/main/ir2dtf.m) and ["d2c"](https://de.mathworks.com/help/ident/ref/dynamicsystem.d2c.html) functions to derive the transfer function for the stochastic system. Utilizing ["ir2dtf"](https://github.com/roaked/agv-system-identification/blob/main/ir2dtf.m) involves selecting specific points that yield optimal outcomes, often through trial and error. Once these points are identified, the step response is extracted. This step response provides crucial insights into the dynamic behaviour of the system under stochastic conditions.

![12133](https://live.staticflickr.com/65535/53351811537_b7a02568a5_c.jpg)



{{< katex display >}}
G_{\text{StochasticStep}}(s) = \frac{1.489s+442.7}{s^2+6.077+442.7}

{{< /katex>}}

### 3.3. Levy Method

Likewise:

![12133131](https://live.staticflickr.com/65535/53352690101_595ce79251_c.jpg)

{{< katex display >}}
G_{\text{StochasticLevy}}(s) = \frac{-24.59s+295}{s^2+5.944s+210.8}

{{< /katex>}}


## 4 Results Discussion - Deterministic and Stochastic

Step response comparison between deterministic and stochastic identification:

![83188](https://live.staticflickr.com/65535/53352910833_2ea66cc7da_z.jpg)

The Normalized Root Mean Squared was used to estimate the fitting of the showcased models.

![2138](https://live.staticflickr.com/65535/53352910848_e575ca23a2_c.jpg)

## 5 Stochastic Identification for Discrete Models

The project discussed the focus on discrete representations of stationary stochastic processes, emphasizing attributes up to the second order, like mean and variance. However, it acknowledged that in reality, many stochastic processes display nonstationarity in mean and variance over time.

Given the aforementioned, a shift in the approach was taken, recognizing that relying solely on white noise signals is inadequate for representing stochastic systems affected by external inputs. To address this limitation, it was recommended exploring models integrating both white noise and system dynamics using the [System Identification Toolbox](https://de.mathworks.com/help/ident/). The aim was to remove mean and trend values, employing statistical criteria like AIC or BIC to evaluate the accuracy of estimations. This method intended to capture the intricate relationship between stochastic elements and system dynamics in real-world scenarios.

{{< details "**Diagram:** Stochastic System Block Diagram - (click to expand)" close >}}
![32481](https://live.staticflickr.com/65535/53352910823_4e24d6d5cf_b.jpg)

In the context provided, the output Y(t) is expressed as the product of x(t), representing the response of our system dynamics to the input u(t), and v(t), which constitutes a colored noise stemming from e(t), characterized as white noise. This formulation underscores the interplay between the system's response to the input and the influence of colored noise, derived from a white noise source, in generating the overall output.

{{< /details >}}

### 5.1. Simulating through System Identification Toolbox


A dataset with a known square response was used as "training data," with trends and mean values removed to create estimations. Validation was then conducted using three separate datasets, evaluating the accuracy of estimations through AIC and BIC criteria. This process assessed how well the model derived from the "training data" performed across different datasets.

![214717](https://live.staticflickr.com/65535/53352910813_6af48e868a_c.jpg)

Although this system is extremely non-linear, an estimation that had a high order for simplicity was avoided.

![21414444](https://live.staticflickr.com/65535/53352690066_75c971f4cb_z.jpg)

### 5.2. ARX - Autoregressive Model with Exogenous Input

{{< details "**Diagram:** ARX Block Diagram - (click to expand)" close >}}
![32481](https://live.staticflickr.com/65535/53352690061_816277f2fa_b.jpg)
{{< /details >}}

This model clearly indicates that both the system and noise dynamics are not entirely independent, sharing common poles represented by A(z^-1). Despite being the simplest among the models, it features an unrealistic connection between the system and noise dynamics. This coupling can be lessened in cases where the signal-to-noise ratio is sufficiently high, offering a potential way to mitigate this issue. Validation with a different experiment but also white noise and multiple powers for a square response:

{{< details "**Validation:** ARX Model for Data 1 - (click to expand)" close >}}
![21381](https://live.staticflickr.com/65535/53353016619_19ed717880_b.jpg)


Additional statistics criterions for this dataset:


| DATA1 POWER3 | BIC    | AIC    | Best Fit |
|--------------|--------|--------|----------|
| ARX122       | 1323.5 | 1305.6 | 29.64    |
| ARX131       | 1332.5 | 1307.5 | 31.54    |
| ARX231       | 1191.7 | 1163.1 | 52.9     |
| ARX113       | 1314.0 | 1303.3 | 23.1     |
| ARX331       | 1196.9 | 1164.7 | 53.5     |
| ARX341       | 1190.3 | 1151.0 | 49.71    |
| ARX322       | 1192.3 | 1163.7 | 53.66    |
| ARX451       | 1197.2 | 1147.0 | 53.46    |
| ARX551       | 1187.9 | 1134.2 | 50.89    |
| ARX351       | 1194.3 | 1147.7 | 54.52    |
{{< /details >}}

{{< details "**Validation:** ARX Model for Data 2 - (click to expand)" close >}}
![213841411](https://live.staticflickr.com/65535/53353016574_83bc58bc95_b.jpg)

Additional statistics criterions for this dataset:


| DATA2 POWER5 | BIC    | AIC    | Best Fit |
|--------------|--------|--------|----------|
| ARX122       | 1022.2 | 1005.7 | 22.35    |
| ARX131       | 1030.2 | 1007.1 | 25.93    |
| ARX231       | 906.5  | 880.1  | 50.81    |
| ARX113       | 1013.1 | 1003.2 | 19.44    |
| ARX331       | 911.7  | 882.0  | 51.14    |
| ARX341       | 904.8  | 868.6  | 51.65    |
| ARX322       | 907.3  | 880.9  | 50.61    |
| ARX451       | 906.8  | 861.0  | 56.94    |
| ARX551       | 894.9  | 845.4  | 59.93    |
| ARX351       | 907.1  | 864.2  | 55.59    |

{{< /details >}}

{{< details "**Validation:** ARX Model for Data 3 - (click to expand)" close >}}
![21381](https://live.staticflickr.com/65535/53353140130_aec5fe8c37_b.jpg)

Additional statistics criterions for this dataset:


| DATA3 POWER2 | BIC    | AIC    | Best Fit |
|--------------|--------|--------|----------|
| ARX122       | 1067.9 | 1051.4 | 16.46    |
| ARX131       | 1076.5 | 1053.5 | 18.56    |
| ARX231       | 951.7  | 925.3  | 40.89    |
| ARX113       | 1059.1 | 1049.2 | 14.53    |
| ARX331       | 950.6  | 921.0  | 41.41    |
| ARX341       | 942.8  | 907.0  | 42.02    |
| ARX322       | 945.5  | 919.1  | 41.39    |
| ARX451       | 939.1  | 893.0  | 41.39    |
| ARX551       | 932.5  | 883.0  | 51.56    |
| ARX351       | 937.3  | 894.4  | 44.02    |

{{< /details >}}


 ARX551, ARX451 and ARX351 always had the best fit, lowest AIC and BIC. Thus, the best approximations. The higher the order of the poles/zeros the closer it gets to the real approximation. Hence, returning better results.

 ![1234199](https://live.staticflickr.com/65535/53352910788_ba9b0fd546.jpg)

 ### 5.3. ARMAX - Autoregressive Model with Exogenous Input

{{< details "**Diagram:** ARMAX Block Diagram - (click to expand)" close >}}
![32422281](https://live.staticflickr.com/65535/53351811487_bdf9437926_b.jpg)
{{< /details >}}

Unlike the ARX model, this particular model accounts for the presence of a moving average component within the noise dynamics, in addition to its connection with the system's dynamics. This model proves valuable in scenarios where dominant disturbances enter the system early in the process. Its consideration of both the system's dynamics and the moving average component in noise dynamics allows for a more accurate representation, especially when early disturbances significantly impact the overall system behaviour. Using the same technology as before...

{{< details "**Validation:** ARMAX Model for Data 1 - (click to expand)" close >}}
![21381](https://live.staticflickr.com/65535/53353140120_49d7c979c6_b.jpg)


Additional statistics criterions for this dataset:


| DATA1 POWER3 NS | BIC    | AIC    | Best Fit |
|-----------------|--------|--------|----------|
| ARMX6260        | 934.69 | 868.7  | 46.55    |
| ARMX5250        | 921.9  | 865.8  | 5.996    |
| ARMX7321        | 919.7  | 857.1  | 48.30    |
| ARMX5111        | 950.2  | 910.6  | 18.43    |
| ARMX3251        | 934.8  | 885.3  | 45.46    |
| ARMX3111        | 936.8  | 910.4  | 48.21    |
| ARMX3241        | 928.4  | 885.5  | 45.34    |
| ARMX2120        | 995.5  | 972.4  | 37.54    |
| ARMX5321        | 906.2  | 856.7  | 48.20    |
| ARMX4331        | 905.2  | 859.0  | 48.94    |

{{< /details >}}

{{< details "**Validation:** ARMAX Model for Data 2 - (click to expand)" close >}}
![213841411](https://live.staticflickr.com/65535/53353016589_db46b9eb19_b.jpg)

Additional statistics criterions for this dataset:

| DATA2 POWER5 | BIC    | AIC    | Best Fit |
|--------------|--------|--------|----------|
| ARMX6260     | 978.3  | 912.4  | 62.43    |
| ARMX5250     | 1068.9 | 1012.9 | 11.96    |
| ARMX7321     | 894.4  | 831.7  | 64.99    |
| ARMX5111     | 1063.7 | 1024.1 | 22.18    |
| ARMX3251     | 999.9  | 950.4  | 55.18    |
| ARMX3111     | 1051.3 | 1024.9 | 58.45    |
| ARMX3241     | 1000.7 | 957.9  | 55.01    |
| ARMX2120     | 1030.7 | 1007.6 | 49.49    |
| ARMX5321     | 888.5  | 839.0  | 60.27    |
| ARMX4331     | 908.4  | 862.2  | 59.24    |


{{< /details >}}

{{< details "**Validation:** ARMAX Model for Data 3 - (click to expand)" close >}}
![21381](https://live.staticflickr.com/65535/53353016584_778432c864_b.jpg)

Additional statistics criterions for this dataset:


| DATA3 POWER2 | BIC    | AIC    | Best Fit |
|--------------|--------|--------|----------|
| ARMX6260     | 977.4  | 911.5  | 60.59    |
| ARMX5250     | 966.8  | 910.7  | 36.92    |
| ARMX7321     | 915.6  | 852.9  | 55.84    |
| ARMX5111     | 979.0  | 939.4  | 43.46    |
| ARMX3251     | 962.4  | 913.0  | 55.03    |
| ARMX3111     | 951.5  | 925.1  | 56.08    |
| ARMX3241     | 954.4  | 911.5  | 54.23    |
| ARMX2120     | 954.1  | 931.0  | 57.90    |
| ARMX5321     | 914.8  | 865.4  | 51.24    |
| ARMX4331     | 930.8  | 884.6  | 50.04    |

{{< /details >}}

Compared to the ARX model, there is a slight increase in the best fit which corresponds to a smaller value in the AIC and BIC criterions. The step response of 3 best approximations in data 2 are given by: 

![29292929](https://live.staticflickr.com/65535/53352689991_56acf03220.jpg)

### 5.4. BJ - Box-Jenkins Model

{{< details "**Diagram:** Box-Jenkins Block Diagram - (click to expand)" close >}}
![324222222281](https://live.staticflickr.com/65535/53353016539_e01e572524_b.jpg)
{{< /details >}}

The Box-Jenkins model is useful when you have disturbances that enter later in the process, e.g. measurement noise on the output. 

{{< details "**Validation:** Box-Jenkins Model for Data 1 - (click to expand)" close >}}
![2138241411](https://live.staticflickr.com/65535/53353016554_c7e2f07de4_b.jpg)

{{< /details >}}

{{< details "**Validation:** Box-Jenkins Model for Data 2 - (click to expand)" close >}}
![2134214124181](https://live.staticflickr.com/65535/53352910743_878538f46f_b.jpg)
{{< /details >}}

{{< details "**Validation:** Box-Jenkins Model for Data 3 - (click to expand)" close >}}
![2134214124181](https://live.staticflickr.com/65535/53352689986_8895050637_b.jpg)
{{< /details >}}

Best result was from data 2. 

{{< details "**Best Fit - Data 2:** Statistical Results - (click to expand)" close >}}
| DATA2 POWER2 | BIC    | AIC    | Best Fit |
|--------------|--------|--------|----------|
| BJ22221      | 870.4  | 831.7  | 73.04    |
| BJ23331      | 894.3  | 839.6  | 51.28    |
| BJ23531      | 903.2  | 835.5  | 54.69    |
| BJ24331      | 897.0  | 835.8  | 50.87    |
| BJ24531      | 948.3  | 877.4  | 51.06    |
| BJ24731      | 919.9  | 836.1  | 48.28    |
| BJ33531      | 868.9  | 798.0  | 64.92    |
| BJ33731      | 876.8  | 793.0  | 73.34    |
| BJ35731      | 882.2  | 792.0  | 71.65    |
| BJ45731      | 893.3  | 796.6  | 72.59    |

{{< /details >}}

Step response:

![2141940](https://live.staticflickr.com/65535/53351811427_b787a0cf83.jpg)

### 5.5. Output-Error Model

{{< details "**Diagram:** Output-Error Block Diagram - (click to expand)" close >}}
![3242222wa22281](https://live.staticflickr.com/65535/53352910738_e0a55196ef_b.jpg)
{{< /details >}}

System and noise dynamics are modelled separately, using zero parameters to model the disturbance. This disturbance can often be assumed to be white noise.

{{< details "**Validation:** Output-Error Model for Data 1 - (click to expand)" close >}}
![2138241411](https://live.staticflickr.com/65535/53353140070_9a3caeffd4_b.jpg)

{{< /details >}}

{{< details "**Validation:** Output-Error Model for Data 2 - (click to expand)" close >}}
![2134214124181](https://live.staticflickr.com/65535/53352689931_c8be807652_b.jpg)
{{< /details >}}

{{< details "**Validation:** Output-Error Model for Data 3 - (click to expand)" close >}}
![2134214124181](https://live.staticflickr.com/65535/53353140050_14eebb16aa_b.jpg)
{{< /details >}}

Best result was from data 3. 

{{< details "**Best Fit - Data 3:** Statistical Results - (click to expand)" close >}}
| DATA3 POWER2 | BIC    | AIC    | Best Fit |
|--------------|--------|--------|----------|
| OE592        | 1074.6 | 998.7  | 75.11    |
| OE472        | 1063.6 | 1004.2 | 75.43    |
| OE352        | 1077.0 | 1034.1 | 71.16    |
| OE252        | 1032.6 | 993.1  | 77.03    |
| OE341        | 1064.6 | 1028.3 | 72.29    |
| OE262        | 1038.6 | 992.5  | 65.12    |
| OE342        | 1110.6 | 1074.3 | 60.56    |
| OE232        | 1652.6 | 1636.1 | 56.15    |
| OE231        | 1199.1 | 1172.7 | 57.46    |
| OE121        | 1257.6 | 1241.1 | 60.60    |
{{< /details >}}


Step response:

![1248](https://live.staticflickr.com/65535/53351811392_a5512b7f2c_z.jpg)


### 5.6. Stochastic Process Analysis

The simulation process involved a trial and error method, initially assuming the input of all stochastic data to be white noise, although this assumption isn't entirely accurate in reality. Iterations were conducted until the expected results were achieved, with validation performed across three distinct data types. From this analysis, several conclusions emerged.

Both Box-Jenkins and Output-Error models exhibited the best fitting values, which was expected due to their higher complexity, allowing for greater precision. However, it's noticeable that increased model complexity, indicated by higher numbers of poles and zeros, led to better fits but also increased overall complexity, which isn't always advantageous.

The simpler ARX and ARMAX models showed fitting slightly above 60%, whereas the more complex models reached around 75%. Additionally, models with lower AIC and BIC values corresponded to fewer parameters needed for estimation, quicker convergence, and more accuracy due to lower residuals. Yet, these criteria are based on assumptions that often don't hold in practical scenarios.

This notion was reinforced by examining the step response plots of the best fitting models. The ARX and ARMAX models exhibited overshoot and steady-state error, as expected due to their simplicity. On the other hand, the OE/BJ models showed some overshoot but minimal steady-state error, indicating their greater complexity allows for a more refined representation with improved performance characteristics. The stable continuous transfer function given by the best fitting model goes as follows:

{{< katex display >}}
G_{\text{Output-Error}}(s) = \frac{0.6447s^4 + 75.98s^3 + 4005s^2 + 8.577 \times 10^4 s + 8.386 \times 10^4}{s^5 + 21.95s^4 + 1039s^3 + 7891s^2 + 6.995 \times 10^4 s + 7.87 \times 10^4}
{{< /katex >}}


## 6 Outcomes

For a final comparison: here is the fitting 3 transfer functions that were obtained for deterministic and stochastic systems.


{{< katex display >}}
G_{\text{output-error}}(s) = \frac{0.6447s^4 + 75.98s^3 + 4005s^2 + 8.577 \times 10^4 s + 8.386 \times 10^4}{s^5 + 21.95s^4 + 1039s^3 + 7891s^2 + 6.995 \times 10^4 s + 7.87 \times 10^4}
{{< /katex >}}


{{< katex display >}}
G_{\text{deterministicLevy}}(s) = \frac{3085s^2 + 3.098 \times 10^5 s + 5.176 \times 10^6}{s^3 + 123s^2 + 2864s +6.932 \times 10^4}
{{< /katex >}}

{{< katex display >}}
G_{\text{stochasticLevy}}(s) = \frac{-24.59s + 295}{s^2 + 5.944s +210.8}
{{< /katex >}}

{{< katex display >}}
G_{\text{stochasticStep}}(s) = \frac{0.1554s + 45.82}{s^2 + 6.077s +442.7}
{{< /katex >}}

{{< katex display >}}
G_{\text{deterministicStep}}(s) = \frac{309.8 s^3 +7682s^2 + 4.842 \times 10^5 s +1.745 \times 10^6}{18.84s^4 + 801.3 s^3 + 991.6 s^2 + 1.868 \times 10^4 s +1.745 \times 10^6}
{{< /katex >}}

As for the step response for the best fitting of the estimated models for each dataset:


{{< details "**Models Validation:** Step response for Data1 - (click to expand)" close >}}
![213424214114124181](https://live.staticflickr.com/65535/53352910678_3168b974b6_c.jpg)
{{< /details >}}

{{< details "**Models Validation:** Step response for Data2 - (click to expand)" close >}}
![2134214125154181](https://live.staticflickr.com/65535/53351811397_d69c479b29_c.jpg)
{{< /details >}}

{{< details "**Models Validation:** Step response for Data3 - (click to expand)" close >}}
![21342141aa24181](https://live.staticflickr.com/65535/53353016489_27c6f842df_c.jpg)
{{< /details >}}


From the depicted graphs, it's evident that the superior models tend to achieve a best fit of approximately 70%. This lower performance in stochastic models might be attributed to the relatively reduced complexity of the attained transfer functions.


## 7 Recap 

The methods applied successfully identified the Rasteirinho system, albeit encountering some challenges. For example,  ncreasing model complexity often improves results but sacrifices simplicity.

![jewjwj](https://i.makeagif.com/media/3-26-2018/1tOBXJ.gif)

Various factors affected data quality, including system noise, uncertainty, and nonlinearities. Errors in data were attributed to lab lighting, low camera frame rates, and Rasteirinho's compliance, causing significant vibrations in the camera mounted with duct tape.

Deterministic identification, especially in the time domain, yielded promising outcomes. However, the stochastic identification using continuous models, both in time and frequency domains, didn't meet expectations during validation with diverse data. This discrepancy could be attributed to potential errors in function implementation. The anticipation that stochastic identification via correlation would be more accurate due to its noise-filtering nature wasn't entirely realized, as the differences between impulse responses obtained from deconvolution and correlation were relatively minor.

On the other hand, stochastic identification using discrete models of varying complexity showed promising results.

## 8 Future Work...

{{< hint example >}}

- Refinement of Stochastic Identification: Addressing errors in function implementation and exploring alternative stochastic identification methods could enhance accuracy.
- Noise Mitigation Techniques: Exploring advanced filtering or noise reduction approaches could improve signal quality in stochastic identification.
- Enhanced Data Collection: Improving data acquisition methods, such as stabilizing the camera or optimizing lighting conditions, can minimize data errors.
- Model Optimization: Continuing to balance model complexity and accuracy to find the optimal trade-off between a model's intricacy and its performance.

{{< /hint >}}
