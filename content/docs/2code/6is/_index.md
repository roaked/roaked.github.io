---
weight: 6
title: "AGV: System Identification"
---

# **System Identification: Analysis of AGV "Rasteirinho" Dynamic System**

![raster](https://live.staticflickr.com/65535/53353039340_0f9b1f0747_w.jpg)

It was aimed to comprehensively analyze the behaviour of the dynamic system "Rasteirinho," initially proposed for studying its "control system". The primary objective was to discern the impact of specific inputs on the system's behaviour. To achieve this, real-time simulations were executed using Matlab and relevant toolboxes such as the [System Identification Toolbox](https://de.mathworks.com/help/ident/). These simulations allowed the acquisition of transfer functions employing diverse time and frequency-based methodologies.

Subsequently, I would classify it in the following sections:

- Deterministic Systems Analysis: The preliminary focus was on deterministic systems, where a consistent input produced a predictable output. This phase provided foundational insights into the system's response under controlled and repeatable conditions.

- Transition to Stochastic Systems: The investigation then progressed to stochastic systems, encompassing both continuous and discrete models. Contrary to deterministic systems, stochastic processes introduced unpredictability. Notably, experiments employing filtered white noise as input demonstrated varying reactions across iterations, emphasizing the inherent stochastic nature of the system.

- Data Collection and Analysis: Data collected from these experiments formed the basis for analysis, leveraging a spectrum of methodologies and tools available in Matlab. The diverse approaches facilitated a comprehensive examination of the system's behavior under stochastic conditions.

The discussions and conclusions drawn stem from the outcomes obtained through multiple analytical methods. The variability observed in stochastic systems prompted insightful considerations regarding the "Rasteirinho" system's behaviour and characteristics. How this variability impacts the overall understanding and analysis of the system becomes a pivotal point for consideration and further exploration.

**Recommendations for Future Study:**
Further investigation into the implications of stochastic behaviour on control mechanisms within the "Rasteirinho" system could deepen our understanding. Exploring advanced analytical techniques and expanding the scope of experiments might offer more nuanced insights into the system's behaviour.

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


#### 2.1.1. Deconvolution Method


The deconvolution method is being employed to derive the impulse response by deconvoluting the convolution integral. The input and output data from the step function are utilized to compute the impulse response, which is subsequently compared with the impulse response obtained from the transfer function using the [System Identification Toolbox](https://de.mathworks.com/help/ident/). 


![213](https://live.staticflickr.com/65535/53353070530_0cdb3fc4ed_z.jpg)


Notably, while similarities are evident, discrepancies exist in the responses due to several reasons. The deconvolution method proves highly sensitive to measurement noise, encountering challenges with an ill-conditioned, [unsymmetrical Toeplitz matrix](https://en.wikipedia.org/wiki/Toeplitz_matrix) 'u.' Moreover, the impulse response, derived as the derivative of the step response, tends to amplify noise.

Following this comparison, the 'ir2dtf' and 'd2c' functions are employed to derive the step response of the system identified through deconvolution. This process involves resampling the deconvolution data and carefully selecting the most representative points from the transient response. The choice of a 4th-order system proves optimal for achieving favourable outcomes. The resulting step response is subsequently obtained for further analysis.

![2212113](https://live.staticflickr.com/65535/53352842998_f87cea2d5a_c.jpg)

Yielding:

{{< katex display >}}
G_{\text{deterministicStep}}(s) = \frac{309.8s^3 + 7682s^2 + 4.842 \times 10^5s + 1.745 \times 10^6}{18.84s^4 + 801.3s^3 + 991.6s^2 + 1.868 \times 10^4s + 1.745 \times 10^6}
{{< /katex >}}


Subsequently, comparing the impulse responses of the different methods and assess their validity:

![22121122113](https://live.staticflickr.com/65535/53353070540_078ba452c4_c.jpg)

Despite the discrepancies evident in the graphs, a notable convergence emerges. Primarily, the significant deviation at the initial peak possibly arises from the amplified noise during the deconvolution process. However, upon a closer examination of the remaining parameters, notable similarities become apparent. The graphs demonstrate congruent peak times and settling times, signifying stability across all observations.


#### 2.1.2. Levy Method

The Levy Method, designed to minimize error between data and estimated model frequency responses, employs curve fitting in amplitude and phase within the numerical frequency domain. This method's utilization of Bode plots enables a comprehensive comparison of results as follows:

![2212112212113](https://live.staticflickr.com/65535/53352621011_d693246b00_c.jpg)

Prior to applying the Levy function, a crucial step involved computing the Fourier transform of the deconvolution. This was achieved using a bespoke function that employs the discrete Fourier transform algorithm. This function determined the gain, phase, and associated frequencies from the acquired points, serving as inputs for the subsequent Levy function.

Upon examination, it became apparent that higher frequency values within the Fourier Transform failed to accurately represent the system dynamics. Despite attempts to represent the initial 10 points, not all points aligned on the phase diagram due to disparities. In the magnitude plot, discrepancies were also notable for higher frequencies. These discrepancies stem from the Rasteirinho's slower system response, where noise interference significantly impacts higher frequencies. Consequently, the initial few points of the Fourier Transform were selectively used to generate the Bode plot via the Levy method.

Following the execution of the Levy function, the presence of unstable poles within the transfer function needed rectification. This requirement arose from occasional instability in the estimated function, a common occurrence within the numerical nature of the Levy method — an aspect encountered during analysis.

![2212421112212113](https://live.staticflickr.com/65535/53353070520_4f3d396b09_c.jpg)

The comparative plot above offers a glimpse into step responses acquired via distinct methodologies (Levy, Matlab Sim toolbox and decovolution), unveiling a remarkable resemblance in their behaviour. However, notable distinctions arise, particularly in the deconvolution method, which exhibits a pronounced overshoot peak. This discrepancy suggests a heightened susceptibility to noise interference within the deconvolution process. Consequently, the resulting transfer function obtained from these analyses is as follows:

{{< katex display >}}
G_{\text{LevyData1}}(s) = \frac{3085s^2 + 3.098 \times 10^5s + 5.176 \times 10^6}{s^3 + 123s^2 + 2864s + 6.932 \times 10^4}
{{< /katex>}}


![221242111221241113](https://live.staticflickr.com/65535/53352948109_a2f9d5ab54_c.jpg)

### 2.2. Data Analysis and Assessment

The prior findings stemmed from the analysis of a singular transient response, selected from the numerous step inputs showcased previously. Considering multiple available plots and simulated square functions, it becomes imperative to validate the conclusions drawn from the chosen transient response against the entirety of the responses.

These initial results, derived from the initial experiment, will now undergo comparison with the other two initially collected data samples. This comparative analysis aims to ascertain the continued validity of our conclusions across the spectrum of responses gathered, ensuring the robustness and generalizability of our findings.


{{< katex display >}}
G_{\text{data2Deconv}}(s) = \frac{368.5s^3 + 412.6s^2 + 1.288 \times 10^5s + 4.188 \times 10^5}{s^4 + 23.09s^3 + 512.3s^2 + 6501s + 2.957 \times 10^4}

G_{\text{data3Deconv}}(s) = \frac{161.6s^3 + 2236 + 1.804 \times 10^5s + 1.508 \times 10^5}{s^4 + 14.59s^3 + 723s^2 + 5760s + 6.186 \times 10^4}

{{< /katex>}}

![221242111221215141113](https://live.staticflickr.com/65535/53352948114_c8450a4b36_z.jpg)

The comparison conducted via the Step Method using deconvolution and estimation via the [System Identification Toolbox](https://de.mathworks.com/help/ident/) appears valid, despite a slight frequency offset observed in the step responses across different data samples. These discrepancies can be attributed to disturbances impacting the system dynamics, altering an array of variables in real-time. Consequently, achieving identical step responses from diverse data samples becomes entirely unattainable.

## 3 Stochastic Identification