---
title: Drone Computer Vision
weight: 5
---

# **Computer Vision for Real-Time Obstacle Detection**

# 1 Description

In preparation for the Red Bull drone race, a comprehensive dataset was compiled, consisting of images capturing gates from diverse angles, distances, and lighting conditions. These images showcase the gates amidst various backgrounds, providing a range of scenarios that drones might encounter during the race. The intention behind gathering such a varied dataset is to enable the development of an algorithm capable of accurately identifying these gates despite changes in perspective, distance, lighting, and background colors, facilitating precise navigation for the drones during the high-paced race.

# 2 Gate Characterization

The analysis of the images reveals that the gates used for detection exhibit a shallow square shape, predominantly appearing in shades of blue along their sides and darker tones overall. Each gate features white letters and small squares within them, affixed to a metal structure supported by two ground-based supports. While the gates primarily share a base color, variations in lighting conditions result in perceptible differences. For instance, exposure to red light causes the white small squares to take on a red hue, as follows:

{{< summary "test" >}}
![nodafafnlfasr](https://live.staticflickr.com/65535/53348683291_d2191e7265.jpg)
{{< /summary >}}

## 2.1 Colorspace Region Segmentation



The initial phase involves image segmentation, commencing with a test set of images showcase below. These specific images were chosen due to their similarity in appearance, presenting a frontal view to the camera with minimal distortion and consistent colors unaffected by varying lighting conditions. This selection aims to establish a baseline for segmentation, allowing for a focused and controlled analysis of gate recognition and isolation within these images.

{{< summary "test" >}}
![ntetete](https://live.staticflickr.com/65535/53348683301_ccf7290b14.jpg)
{{< /summary >}}

The strategy involved an examination of different color spaces to identify potential relationships between them. It was done by observing the RGB color space aiming to discern any possible connections or patterns among the color channels within the images.

{{< summary "test" >}}
![ntetesaddate](https://live.staticflickr.com/65535/53347810927_ffbb57983a_z.jpg)
{{< /summary >}}

In the image, it is seen the original RGB components as they are, without any changes. It's hard to spot the gate distinctly from the background in any of these components. None of them make the gate stand out separately. So, it looks like using these RGB components isn't the best for separating the gate from the rest. Next up was trying out the HSV color representation.

{{< summary "test" >}}
![ntetsdsdaete](https://live.staticflickr.com/65535/53349006219_f418323e63.jpg)
{{< /summary >}}

In the HSV representation, an adjustment was made to the original images specifically for the Hue component. The process involved converting the image to the HSV color space, modifying its Saturation and Value components to a saturation of 1, and then re-converting the image back to the RGB color space. This resulted in a representation highlighting the genuine colors of the image, with only the Hue component "enabled".

When the image is observed, the gate is distinctly noticeable from the background in the Hue component. The dark and blue tones from the original picture are reflected as dark blue in the Hue representation, while the small white squares take on a magenta hue. The background spans a range from light blue to green and red in this representation. While the Saturation and Value components alone do not facilitate gate identification.

{{< summary "test" >}}
![ntetsdsdaeasate](https://live.staticflickr.com/65535/53348700101_af30a3ae2e_c.jpg)
{{< /summary >}}

{{< summary "test" >}}
![ntetsdsssssdaeasate](https://live.staticflickr.com/65535/53349023269_7ae02dbe61_c.jpg)
{{< /summary >}}

The Lab and YCbCr representations didn't yield satisfactory results as the distinction between the background and the gate was hardly noticeable. Consequently, the HSV color space emerged as the most effective choice for segmenting the gate in the images.

To execute the segmentation, the images from the test dataset were examined in their HSV representations. Through visual inspection and some trial and error, specific conditions (thresholds) for the HSV values were established to pinpoint the gate's location.

This process generated a black and white image where all the points meeting the conditions were highlighted. However, restricting the HSV values alone proved insufficient. Some parts of the background shared similar HSV values with the gate, leading to noise in the resulting images.

## 2.2 Black and White Image Segmentation

Applying thresholds to the HSV values, a first approximation of the gate can be found.


{{< summary "test" >}}
![ntetsdss3223sssdaeasate](https://live.staticflickr.com/65535/53349023254_37cce63789.jpg)
{{< /summary >}}

To refine the image, several morphological operations need to be applied. In exploring various techniques, one solution considered was to initiate the noise reduction process by cropping the initial image. Utilizing a cropping function facilitated the selection of a specific region of interest (ROI). An illustration of this method is depicted in the subsequent figure.

{{< summary "test" >}}
![ntetsdss3223sss2ssdaeasate](https://live.staticflickr.com/65535/53349023284_ae24b7b46a.jpg)
{{< /summary >}}

While the outcomes appeared acceptable for the selected images in the testing set where the gate was in proximity to the camera, this method was ultimately dismissed. Its limitation became apparent with images where the gate was situated farther away, rendering the approach less effective. Moreover, in real-life scenarios involving drones that sway considerably from side to side, cropping the image and narrowing the field of vision was deemed risky. This approach could potentially result in the drone losing track of the gate entirely, which is an undesirable outcome.


## 2.3 Morphological Operations

To isolate the gate and eliminate unwanted noise, a shift in approach was necessary, focusing on morphological operations. For the test image set, various scenarios were taken into account based on the image conditions. In the general case, the following morphological operations were applied:

- **1.** Small dilation operation: Utilizing the [MATLAB function 'imdilate'](https://www.mathworks.com/help/images/ref/imdilate.html), this operation aimed to connect isolated pixels located at the corners of the gate with the rest of the gate. The 'cube' structure with a magnitude of 2 was employed for this purpose.

- **2.** Retaining the largest area: This step involved employing the [MATLAB function 'bwareafilt'](https://www.mathworks.com/help/images/ref/bwareafilt.html) to retain solely the gate in the black and white image. Initially, all connected components within a specified range were extracted, followed by preserving solely the largest area. This process became necessary due to potential noise accumulation and formation of objects in the image caused by the dilation process in certain cases.