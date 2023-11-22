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

- **1.** Small dilation operation: Utilizing the [MATLAB function *'imdilate'*](https://www.mathworks.com/help/images/ref/imdilate.html), this operation aimed to connect isolated pixels located at the corners of the gate with the rest of the gate. The 'cube' structure with a magnitude of 2 was employed for this purpose.

{{< summary "test" >}}
![ntetsdss3223sssss2ssdaeasate](https://live.staticflickr.com/65535/53349067404_f2a8d9eded.jpg)
{{< /summary >}}


- **2.** Retaining the largest area: This step involved employing the [MATLAB function *'bwareafilt'*](https://www.mathworks.com/help/images/ref/bwareafilt.html) to retain solely the gate in the black and white image. Initially, all connected components within a specified range were extracted, followed by preserving solely the largest area. This process became necessary due to potential noise accumulation and formation of objects in the image caused by the dilation process in certain cases.

{{< summary "test" >}}
![ntetsdss3223sssss2ssdaeas22ate](https://live.staticflickr.com/65535/53348976298_3cd07df4ea.jpg)
{{< /summary >}}

- **3.** Closing small holes: The [MATLAB function *'imclose'*](https://www.mathworks.com/help/images/ref/imclose.html) function was employed to close only the small holes that form part of the gate shape. This operation aimed to fill these small gaps within the gate structure. A closing operation with a magnitude of 80 was applied using the 'cube' structure to ensure closure when the gate shape wasn't entirely closed (e.g., when the rectangle wasn't fully closed). It's worth noting that the [MATLAB function *'imfill'*](https://www.mathworks.com/help/images/ref/imfill.html) function was avoided to prevent inadvertently filling the actual opening of the gate where drones navigate through.

{{< summary "test" >}}
![ntetsdss3223sssss2ssdae11as22ate](https://live.staticflickr.com/65535/53349194590_8878fdac83.jpg)
{{< /summary >}}

- **4.** Smoothing the region: To further refine the image and eliminate residual noise, an [MATLAB function *'imopen'*](https://www.mathworks.com/help/images/ref/imopen.html) operation was executed using the 'cube' structure with a magnitude of 20. This process aids in cleaning up any remaining artifacts or noise in the image. Notably, employing a higher magnitude might risk reopening the gate's rectangular structure itself, hence the careful choice of parameters for this operation.

{{< summary "test" >}}
![ntetsdss3223ssss221s2ssdae11as22ate](https://live.staticflickr.com/65535/53348744306_80780ae17a.jpg)
{{< /summary >}}


- **5.** Reapplying the mask derived from the obtained black and white image: This was achieved by performing a boolean logic multiplication between the black and white image (acting as a mask) and the original image. The result was a segmented image where the gate was isolated and distinctly visible.

{{< summary "test" >}}
![nte11tsdss3223sss2ssdaeasate](https://live.staticflickr.com/65535/53348744311_051decab50.jpg)
{{< /summary >}}


Another method explored was the superpixels technique which involves segmenting the image. It is a relatively state-of-the-art technique that often couples numerous superpixels with clustering to enhance image recognition.[There are recent papers being published, but a particularly interesting one can be seen here.](https://cran.r-project.org/web/packages/OpenImageR/vignettes/Image_segmentation_superpixels_clustering.html). The divided image contain many superpixels and each superpixel is then represented by the average color value within its region. The intention behind this approach was to implement the superpixels segmentation preceding the HSV segmentation and assess whether this sequential method could yield improved results. The goal was to evaluate if the combination of superpixels segmentation followed by HSV segmentation could enhance the accuracy of isolating the gate in the images.


{{< summary "test" >}}
![ntetsdss3223sss2ssdaeasate111](https://live.staticflickr.com/65535/53349194580_d144f5c7a4.jpg)
{{< /summary >}}


This method helps reducing the number of different colors in the image which could help the segmentation process in some cases.

{{< summary "test" >}}
![ntetsdss3223sss2ss52daeasate111](https://live.staticflickr.com/65535/53349067274_d76b061191.jpg)
{{< /summary >}}


{{< summary "test" >}}
![ntetsdss3223sss2ss52d4121aeasate111](https://live.staticflickr.com/65535/53347872712_3deecf9434.jpg)
{{< /summary >}}

This method was found to be functional but lacked reliability for consistent segmentation. A lower count of superpixels resulted in poorer outcomes, while a higher count led to slower segmentation without notable improvement. Moreover, in some instances, an increased count made segmentation more challenging as the gate and background merged together.

The segmentation algorithm underwent testing on various images. It worked well for images with similar background colors and lighting conditions, often necessitating minor adjustments to the thresholds for optimization. However, for other images, a new set of thresholds had to be determined.

To facilitate threshold selection, two new parameters were introduced to group images with similar colors. One parameter, denoted as 'c', signifies the center of mass of the histogram obtained using the [MATLAB *'imhist'* function](https://de.mathworks.com/help/images/ref/imhist.html). This parameter helped isolate images with pink or red backgrounds, requiring unique thresholds due to their distinct characteristics. Additionally, two images featuring a large red and black banner in the background were also isolated as they demanded slight threshold adjustments.

The second parameter was established to represent the x-coordinate with the maximum value in the histogram obtained from [MATLAB *'imhist'* function](https://de.mathworks.com/help/images/ref/imhist.html). This parameter primarily aided in segregating an image with a blue background. While this image had a 'c' parameter similar to others in the testing set, it necessitated different thresholds owing to the blending of the blue background with the gate's blue.

Images with distinct thresholds also required minor modifications to the order of morphological operations.


## 2.4 Edge Detection
