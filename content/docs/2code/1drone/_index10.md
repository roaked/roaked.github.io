---
title: Drone Computer Vision
weight: 10
---

# **Computer Vision for Real-Time Obstacle Detection**

# 1 Description

In preparation for the Red Bull drone race, a comprehensive dataset was compiled, consisting of images capturing gates from diverse angles, distances, and lighting conditions. These images showcase the gates amidst various backgrounds, providing a range of scenarios that drones might encounter during the race. The intention behind gathering such a varied dataset is to enable the development of an algorithm capable of accurately identifying these gates despite changes in perspective, distance, lighting, and background colors, facilitating precise navigation for the drones during the high-paced race.

# 2 Gate Characterization

The analysis of the images reveals that the gates used for detection exhibit a shallow square shape, predominantly appearing in shades of blue along their sides and darker tones overall. Each gate features white letters and small squares within them, affixed to a metal structure supported by two ground-based supports. While the gates primarily share a base color, variations in lighting conditions result in perceptible differences. For instance, exposure to red light causes the white small squares to take on a red hue, as follows:

{{< summary "test" >}}
![nonlinear](https://live.staticflickr.com/65535/53348683291_d2191e7265.jpg)
{{< /summary >}}

## 2.1 Colorspace Region Segmentation



The initial phase involves image segmentation, commencing with a test set of images showcase below. These specific images were chosen due to their similarity in appearance, presenting a frontal view to the camera with minimal distortion and consistent colors unaffected by varying lighting conditions. This selection aims to establish a baseline for segmentation, allowing for a focused and controlled analysis of gate recognition and isolation within these images.


The strategy involved an examination of different color spaces to identify potential relationships between them. It was done by observing the RGB color space aiming to discern any possible connections or patterns among the color channels within the images.


In the image, it is seen the original RGB components as they are, without any changes. It's hard to spot the gate distinctly from the background in any of these components. None of them make the gate stand out separately. So, it looks like using these RGB components isn't the best for separating the gate from the rest. Next up was trying out the HSV color representation.

