---
title: "Gate Sense: Computer Vision"
weight: 5
---

# **Phase 4. UAV Computer Vision**

{{< hint tip >}}
If you wish to skip the theoretical and implementation details, [please click here to jump to the summarized findings Ch. 3 - Pixel Pilots Certification Acquired](https://ricardochin.com/docs/code/uav/computer-vision/#3-pixel-pilots-certification-acquired). I have cool images! ✌️{{< /hint >}}

## 1 Computer Vision for Obstacle Detection

In preparation for the Red Bull drone race, a comprehensive dataset was compiled, consisting of images capturing gates from diverse angles, distances, and lighting conditions. These images showcase the gates amidst various backgrounds, providing a range of scenarios that drones might encounter during the race. The intention behind gathering such a varied dataset is to enable the development of an algorithm capable of accurately identifying these gates despite changes in perspective, distance, lighting, and background colors, facilitating precise navigation for the drones during the high-paced race.

## 2 Gate Characterization

The analysis of the images reveals that the gates used for detection exhibit a shallow square shape, predominantly appearing in shades of blue along their sides and darker tones overall. Each gate features white letters and small squares within them, affixed to a metal structure supported by two ground-based supports. While the gates primarily share a base color, variations in lighting conditions result in perceptible differences. For instance, exposure to red light causes the white small squares to take on a red hue, as follows:

![nodafafnlfasr](https://live.staticflickr.com/65535/53348683291_d2191e7265.jpg)


### 2.1. Colorspace Region Segmentation



The initial phase involves image segmentation, commencing with a test set of images showcase below. These specific images were chosen due to their similarity in appearance, presenting a frontal view to the camera with minimal distortion and consistent colors unaffected by varying lighting conditions. This selection aims to establish a baseline for segmentation, allowing for a focused and controlled analysis of gate recognition and isolation within these images.


![ntetete](https://live.staticflickr.com/65535/53348683301_ccf7290b14.jpg)


The strategy involved an examination of different color spaces to identify potential relationships between them. It was done by observing the RGB color space aiming to discern any possible connections or patterns among the color channels within the images.


![ntetesaddate](https://live.staticflickr.com/65535/53347810927_ffbb57983a_z.jpg)


In the image, it is seen the original RGB components as they are, without any changes. It's hard to spot the gate distinctly from the background in any of these components. None of them make the gate stand out separately. So, it looks like using these RGB components isn't the best for separating the gate from the rest. Next up was trying out the HSV color representation.


![ntetsdsdaete](https://live.staticflickr.com/65535/53349006219_f418323e63.jpg)

In the HSV representation, an adjustment was made to the original images specifically for the Hue component. The process involved converting the image to the HSV color space, modifying its Saturation and Value components to a saturation of 1, and then re-converting the image back to the RGB color space. This resulted in a representation highlighting the genuine colors of the image, with only the Hue component "enabled".

```matlab
function [seg,BW] = Segmenter(img)

imgHSV = rgb2hsv(img);
hue = imgHSV(:,:,1);
sat = imgHSV(:,:,2);
val = imgHSV(:,:,3);

[counts,binLocations] = imhist(hue);
a = 0;
b = 0;
for i = 1:256
    a = a + binLocations(i)*counts(i);
    b = b + counts(i);
end
c = a/b;
[~,I] = max(counts);
I = I/256;

if c <= 0.15 
    BW = hue >= 0.50 & hue <= 0.60 ...
        | hue >= 0.8 & hue <= 0.990 & val < 0.7 & sat > 0.3 ... 
        | val > 0.9 & sat > 0.8;
    
    BW = imdilate(BW,strel('cube',8));
    BW = bwareafilt(BW,[5000 9999999999]);
    BW = imclose(BW,strel('cube',80));
    BW = imopen(BW,strel('cube',30));
    
elseif 0.15 < c && c <= 0.1913 
    BW = hue >= 0.61 & hue <= 0.65 & sat>=0.30... 
   | hue >= 0.62 & hue <= 0.68 & sat>=0.08 & val > 0.20...
   | hue > 0.68 & hue <= 0.72 & sat < 0.2 ...
   | hue >= 0.0 & hue <= 0.2 & sat<=0.18 & val > 0.55;...
  
    BW = bwareafilt(BW,[5000 9999999999]);
    BW = imdilate(BW,strel('cube',5));
    BW = bwareafilt(BW,1);
    BW = imclose(BW,strel('cube',80));
    BW = imopen(BW,strel('cube',20));
   

elseif 0.20 < c && c < 0.4 && I > 0.15 && I < 0.20
    BW = hue >= 0.65 & hue <= 0.69 & sat>=0.4...
        | hue >= 0.61 & hue <= 0.65 & sat>=0.25 & val >= 0.9 ...
        | hue >= 0.64 & hue <= 0.65 & sat>=0.35 & val <= 0.9;
    
    BW = imdilate(BW,strel('cube',3));
    BW = bwareafilt(BW,[5000 9999999999]);
    BW = bwareafilt(BW,1);
    BW = imclose(BW,strel('cube',80));
    BW = imopen(BW,strel('cube',30));
    BW = imdilate(BW,strel('cube',5));

elseif 0.193 < c && c < 0.55
    BW = hue >= 0.64 & hue <= 0.85 & sat>=0.10 ...
        | hue >= 0.55 & hue <= 0.64 & sat >= 0.4...
        | hue >= 0.45 & hue <= 0.64 & val > 0.99 & sat < 0.4...
        | hue >= 0.68 & hue <= 0.80 & sat<=0.08 & val > 0.4 & val < 0.8;
        
    BW = imdilate(BW,strel('cube',2));
    BW = bwareafilt(BW,[5000 9999999999]);
    BW = bwareafilt(BW,1);
    BW = imclose(BW,strel('cube',80));
    BW = imopen(BW,strel('cube',20));
    BW = bwareafilt(BW,1);

elseif c >= 0.55
    BW = sat >= 0.76;   
    BW = bwareafilt(BW,[5000 9999999999]);
    BW = bwareafilt(BW,1);
    BW = imclose(BW,strel('cube',80));
    BW = imopen(BW,strel('cube',30));
    BW = imdilate(BW,strel('line',30,90));
    BW = imerode(BW,strel('cube',10));
end

seg = img.*uint8(BW);
end
```

When the image is observed, the gate is distinctly noticeable from the background in the Hue component. The dark and blue tones from the original picture are reflected as dark blue in the Hue representation, while the small white squares take on a magenta hue. The background spans a range from light blue to green and red in this representation. While the Saturation and Value components alone do not facilitate gate identification.


![ntetsdsdaeasate](https://live.staticflickr.com/65535/53348700101_af30a3ae2e_c.jpg)



![ntetsdsssssdaeasate](https://live.staticflickr.com/65535/53349023269_7ae02dbe61_c.jpg)


The Lab and YCbCr representations didn't yield satisfactory results as the distinction between the background and the gate was hardly noticeable. Consequently, the HSV color space emerged as the most effective choice for segmenting the gate in the images.

To execute the segmentation, the images from the test dataset were examined in their HSV representations. Through visual inspection and some trial and error, specific conditions (thresholds) for the HSV values were established to pinpoint the gate's location.

This process generated a black and white image where all the points meeting the conditions were highlighted. However, restricting the HSV values alone proved insufficient. Some parts of the background shared similar HSV values with the gate, leading to noise in the resulting images.

### 2.2. Black and White Segmentation

Applying thresholds to the HSV values, a first approximation of the gate can be found.



![ntetsdss3223sssdaeasate](https://live.staticflickr.com/65535/53349023254_37cce63789.jpg)


To refine the image, several morphological operations need to be applied. In exploring various techniques, one solution considered was to initiate the noise reduction process by cropping the initial image. Utilizing a cropping function facilitated the selection of a specific region of interest (ROI). An illustration of this method is depicted in the subsequent figure.

![ntetsdss3223sss2ssdaeasate](https://live.staticflickr.com/65535/53349023284_ae24b7b46a.jpg)


While the outcomes appeared acceptable for the selected images in the testing set where the gate was in proximity to the camera, this method was ultimately dismissed. Its limitation became apparent with images where the gate was situated farther away, rendering the approach less effective. Moreover, in real-life scenarios involving drones that sway considerably from side to side, cropping the image and narrowing the field of vision was deemed risky. This approach could potentially result in the drone losing track of the gate entirely, which is an undesirable outcome.


### 2.3. Morphological Operations

To isolate the gate and eliminate unwanted noise, a shift in approach was necessary, focusing on morphological operations. For the test image set, various scenarios were taken into account based on the image conditions. In the general case, the following morphological operations were applied:

- **1.** Small dilation operation: Utilizing the [MATLAB function *'imdilate'*](https://www.mathworks.com/help/images/ref/imdilate.html), this operation aimed to connect isolated pixels located at the corners of the gate with the rest of the gate. The 'cube' structure with a magnitude of 2 was employed for this purpose.


![ntetsdss3223sssss2ssdaeasate](https://live.staticflickr.com/65535/53349067404_f2a8d9eded.jpg)



- **2.** Retaining the largest area: This step involved employing the [MATLAB function *'bwareafilt'*](https://www.mathworks.com/help/images/ref/bwareafilt.html) to retain solely the gate in the black and white image. Initially, all connected components within a specified range were extracted, followed by preserving solely the largest area. This process became necessary due to potential noise accumulation and formation of objects in the image caused by the dilation process in certain cases.


![ntetsdss3223sssss2ssdaeas22ate](https://live.staticflickr.com/65535/53348976298_3cd07df4ea.jpg)

- **3.** Closing small holes: The [MATLAB function *'imclose'*](https://www.mathworks.com/help/images/ref/imclose.html) function was employed to close only the small holes that form part of the gate shape. This operation aimed to fill these small gaps within the gate structure. A closing operation with a magnitude of 80 was applied using the 'cube' structure to ensure closure when the gate shape wasn't entirely closed (e.g., when the rectangle wasn't fully closed). It's worth noting that the [MATLAB function *'imfill'*](https://www.mathworks.com/help/images/ref/imfill.html) function was avoided to prevent inadvertently filling the actual opening of the gate where drones navigate through.


![ntetsdss3223sssss2ssdae11as22ate](https://live.staticflickr.com/65535/53349194590_8878fdac83.jpg)


- **4.** Smoothing the region: To further refine the image and eliminate residual noise, an [MATLAB function *'imopen'*](https://www.mathworks.com/help/images/ref/imopen.html) operation was executed using the 'cube' structure with a magnitude of 20. This process aids in cleaning up any remaining artifacts or noise in the image. Notably, employing a higher magnitude might risk reopening the gate's rectangular structure itself, hence the careful choice of parameters for this operation.


![ntetsdss3223ssss221s2ssdae11as22ate](https://live.staticflickr.com/65535/53348744306_80780ae17a.jpg)



- **5.** Reapplying the mask derived from the obtained black and white image: This was achieved by performing a boolean logic multiplication between the black and white image (acting as a mask) and the original image. The result was a segmented image where the gate was isolated and distinctly visible.


![nte11tsdss3223sss2ssdaeasate](https://live.staticflickr.com/65535/53348744311_051decab50.jpg)



Another method explored was the superpixels technique which involves segmenting the image. It is a relatively state-of-the-art technique that often couples numerous superpixels with clustering to enhance image recognition. [There are recent papers being published, but a particularly interesting one can be seen here.](https://cran.r-project.org/web/packages/OpenImageR/vignettes/Image_segmentation_superpixels_clustering.html). The divided image contain many superpixels and each superpixel is then represented by the average color value within its region. The intention behind this approach was to implement the superpixels segmentation preceding the HSV segmentation and assess whether this sequential method could yield improved results. The goal was to evaluate if the combination of superpixels segmentation followed by HSV segmentation could enhance the accuracy of isolating the gate in the images.



![ntetsdss3223sss2ssdaeasate111](https://live.staticflickr.com/65535/53349194580_d144f5c7a4.jpg)



This method helps reducing the number of different colors in the image which could help the segmentation process in some cases.


![ntetsdss3223sss2ss52daeasate111](https://live.staticflickr.com/65535/53349067274_d76b061191.jpg)



![ntetsdss3223sss2ss52d4121aeasate111](https://live.staticflickr.com/65535/53347872712_3deecf9434.jpg)


This method was found to be functional but lacked reliability for consistent segmentation. A lower count of superpixels resulted in poorer outcomes, while a higher count led to slower segmentation without notable improvement. Moreover, in some instances, an increased count made segmentation more challenging as the gate and background merged together.


The segmentation algorithm underwent testing on various images. It worked well for images with similar background colors and lighting conditions, often necessitating minor adjustments to the thresholds for optimization. However, for other images, a new set of thresholds had to be determined.

To facilitate threshold selection, two new parameters were introduced to group images with similar colors. One parameter, denoted as 'c', signifies the center of mass of the histogram obtained using the [MATLAB *'imhist'* function](https://de.mathworks.com/help/images/ref/imhist.html). This parameter helped isolate images with pink or red backgrounds, requiring unique thresholds due to their distinct characteristics. Additionally, two images featuring a large red and black banner in the background were also isolated as they demanded slight threshold adjustments.

The second parameter was established to represent the x-coordinate with the maximum value in the histogram obtained from [MATLAB *'imhist'* function](https://de.mathworks.com/help/images/ref/imhist.html). This parameter primarily aided in segregating an image with a blue background. While this image had a 'c' parameter similar to others in the testing set, it necessitated different thresholds owing to the blending of the blue background with the gate's blue.

Images with distinct thresholds also required minor modifications to the order of morphological operations.

![tes24141241t](https://live.staticflickr.com/65535/53349078904_b07f388b95_c.jpg)

**Segmented**

![tes123142t](https://live.staticflickr.com/65535/53349206255_be9c9f9446_c.jpg)


### 2.4. Edge Detection

The next phase involved edge detection to outline the gate's boundaries using various methods of the [MATLAB function *'edge'*](https://de.mathworks.com/help/images/ref/edge.html). The previously obtained segmented image was utilized due to its superior performance in noise removal and clear delineation of the gate.

Three distinct methods were employed:

- **Canny Method**: This technique identifies edges by seeking local maximums of the gradient of the image. The gradient is derived from a Gaussian filter. It uses two thresholds—one for strong edges and another for weak edges. Weak edges are included in the output only if they are connected to strong edges.

![ntetsdss322214143sss2ss52d4121aeasate111](https://live.staticflickr.com/65535/53349009603_1155d7734b_c.jpg)

- **Sobel Operator**: This method convolves the image with a small, separable, and integer-valued filter along the horizontal and vertical directions. While computationally inexpensive, its gradient approximation can be relatively coarse, especially for high-frequency variations in the image.

![ntetsdss322214143sss2ss52412d4121aeasate111](https://live.staticflickr.com/65535/53348778536_65dbe9efb7_b.jpg)

- **Laplacian Filters**: These filters are derivative-based and designed to detect areas of rapid change (edges) in images. As derivative filters are sensitive to noise, it's common practice to smooth the image (e.g., using a Gaussian filter) before applying the Laplacian. This combined process is known as the Laplacian of Gaussian (LoG) operation:


{{< katex display >}}
L(x, y) = \nabla^2 f(x, y) = \frac{\partial^2 f(x, y)}{\partial x^2} + \frac{\partial^2 f(x, y)}{\partial y^2}
{{< /katex >}}

The representation of the 2D LoG (Laplacian of Gaussian) function centered on zero and with a Gaussian standard deviation {{< katex >}}\sigma{{< /katex >}} is given by:

{{< katex display >}}
\text{LoG}(x, y) = -\frac{1}{\pi \sigma^4} \left(1 - \frac{x^2 + y^2}{2\sigma^2}\right) e^{-\frac{x^2 + y^2}{2\sigma^2}}
{{< /katex >}}


![ntetsdss322214143sss142142ss52412d4121aeasate111](https://live.staticflickr.com/65535/53349228340_7bfd0cb588_c.jpg)

Edge detection was carried out on two types of images: the BW mask acquired during the segmentation process and the segmented images. Conducting edge detection on the original images was dismissed due to the excessive background information it would provide.

When performing edge detection on the BW mask, the expected outcome was solely the contours of the mask, encompassing both inner and outer rectangles. Conversely, edge detection on the segmented image aimed to extract additional features such as letters and squares, with the option to adjust the number of extracted features using a threshold.

The results displayed two images: edge detection on the BW mask was shown on the left, while edge detection on the segmented image was depicted on the right. The superiority of the Canny method's results was attributed mainly to the adjustability of its two thresholds.

### 2.5. Hough Transform

The Hough Transform involves identifying lines within an image by grouping edge points into object candidates, which undergo a voting procedure. This process occurs in a parameter space where the object candidates are considered local maxima. Each point in the Hough Space is represented by two parameters, Rho ({{< katex >}}\rho{{< /katex >}}) and Theta ({{< katex >}}\theta{{< /katex >}}). Rho represents the perpendicular distance from the origin to the line, while theta denotes the angle between the x-axis and this vector.

The MATLAB implementation comprises a series of functions. The [MATLAB function *'hough'*](https://de.mathworks.com/help/images/ref/hough.html) computes the Standard Hough Transform (SHT) of a binary image, producing outputs of rho, theta, and H, a matrix representing the parameter space. Subsequently, the [MATLAB function *'houghpeaks'*](https://de.mathworks.com/help/images/ref/houghpeaks.html) locates peaks in the Hough Transform Matrix, typically selecting 20 peaks. Finally, the [MATLAB function *'houghlines'*](https://de.mathworks.com/help/images/ref/houghlines.html) function identifies line segments within the image, returning a structure array that represents the merged line segments.

Hough lines serve as an alternative method for line detection. In this case, the Hough lines were applied to the BW mask generated from the Canny edges, as the Canny method displayed the most promising results. This application aimed to enhance the lines extracted from the edges, particularly emphasizing the extremes of the gate. The results are depicted below.

![hough](https://live.staticflickr.com/65535/53349037313_05364df81a_c.jpg)

### 2.6. Extreme Points and Centroid

The [function *'CornerDetec'*](https://github.com/roaked/redbull-drone-computer-vision/blob/main/CornerDetec.m) was created to calculate the extreme points of the gate image post edge detection. As the gate consistently maintains a square shape, the extreme points can be defined by a sequence of maximum and minimum x and y coordinates. For instance, the top-right corner would be the point where the sum of the x and y coordinates is maximized.

```matlab
function [C] = CornerDetec(BWcanny)

%1. Get rid of the white border
I2 = imclearborder(BWcanny);

%2. Find each of the four corners
[y,x] = find(I2);
[~,loc] = min(y+x);
C = [x(loc),y(loc)];
[~,loc] = min(y-x);
C(2,:) = [x(loc),y(loc)];
[~,loc] = max(y+x);
C(3,:) = [x(loc),y(loc)];
[~,loc] = max(y-x);
C(4,:) = [x(loc),y(loc)];
end
```

Determining the centroid involved using the [MATLAB function *'regionprops'*](https://www.mathworks.com/help/images/ref/regionprops.html). Given the gate's square and axisymmetric shape, the centroid could be extracted using either the inner or outer square of the gate.

Despite prior morphological operations aimed at refining the segmentation quality, residual noise persisted within the images. To reduce the error between the actual centroid and the estimated centroid, the inner square of the gate, less susceptible to edge detection errors, was utilized. In cases where the inner square wasn't entirely identified, the centroid of the outer square was plotted instead.


![linear213312](https://live.staticflickr.com/65535/53349255010_8a1bb87556.jpg)

{{< details "**Results:** Identification of centroid on a different image - (click to expand)" close >}}
![li2434near](https://live.staticflickr.com/65535/53349255005_a19d3154d9_c.jpg)
{{< /details >}}

{{< details "**Results:** Identification of centroid on all images - (click to expand)" close >}}
![li2434nea41421r](https://live.staticflickr.com/65535/53348804846_3f38a86795_c.jpg)
{{< /details >}}

{{< details "**Results:** Identification of hough lines on all images - (click to expand)" close >}}
![li2434n123ea41421r](https://live.staticflickr.com/65535/53349254980_7e1375c0ff_c.jpg)
{{< /details >}}

### 2.7. Image Enhancement

To accentuate the gate within the image, the region corresponding to the gate was emphasized. This was achieved by inverting the mask obtained from the segmentation process. Inverting the mask allowed for the removal of the gate from the original image and isolating it for highlighting purposes. To accomplish this, each color channel of the inverted mask was isolated and subsequently combined to create a true color RGB image.

```matlab
%% Gate Image Enhancement
segWB = img.*uint8(WB);
rgbImage = segWB;
% Extract the individual red, green, and blue color channels.
redChannel = rgbImage(:, :, 1);
greenChannel = rgbImage(:, :, 2);
blueChannel = rgbImage(:, :, 3);
% Find pixels that are pure black - black in all 3 channels.
blackPixels = redChannel == 0 & greenChannel  == 0 & blueChannel  == 0;

redChannel(blackPixels) = 255;
greenChannel(blackPixels) = 0;
blueChannel(blackPixels) = 255;
% Recombine separate color channels into a single, true color RGB image.
rgbImage = cat(3, redChannel, greenChannel, blueChannel);
```

 Additionally, the lines detected from the extreme points were plotted on this highlighted gate region to further outline its boundaries.


![imageenhance](https://live.staticflickr.com/65535/53347932412_12209e04fd_z.jpg)

The conversion from RGB to HSV can be applied to this image. It's important to note that the green lines visible in the figure are not inherent to the image; they are plotted lines and, consequently, do not manifest in the HSV representation.


![imageenh42141ance](https://live.staticflickr.com/65535/53347932407_d2b4da0dd6_c.jpg)

Upon observing the HSV components, it becomes apparent that the gate is distinctly highlighted in the Saturation and Value images. For all the image datasets:

![imageenh42141ance](https://live.staticflickr.com/65535/53347932402_ef0bf9b1d0_z.jpg)



## 3 Pixel Pilots Certification Acquired

It became evident that arriving at a solution wasn't straightforward, and while the obtained solution wasn't perfect, it showcased potential for optimization with an expanded dataset. Additional images would enhance the algorithm's ability to recognize the gate under various lighting conditions and backgrounds. Nonetheless, color segmentation emerged as an effective means to segment the gate.

Among edge detection methods, the Canny approach stood out as the most effective, although other methods also demonstrated reasonable performance. The Hough transform offered an alternative method to identify lines, proving to be a decent performer.

Despite minor imperfections resulting from imperfect segmentation, the detection of extreme points and centroids was generally successful.

Ultimately, in this section the work done successfully achieved its goal by effectively isolating and enhancing the gate. Employing various solutions contributed to solving the challenge of gate identification for drone competitions.


{{< hint example >}}
As for next steps...Possibly in a phase 5. It could involve exploring the integration of machine learning techniques to further enhance the gate identification process, potentially leading to real-time detection capabilities. Machine learning models, such as convolutional neural networks (CNNs) or other deep learning architectures, could be trained on an expanded dataset to improve gate recognition across diverse conditions. This adaptation could pave the way for more robust and efficient gate identification systems, especially in real-time scenarios during drone competitions.{{< /hint >}}