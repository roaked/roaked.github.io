---
title: "Structure From Motion: Camera"
weight: 6
bookHidden: True
---

## 1 Abstract

This research concerns the structure from motion from two views, which is the process of estimating the 3D structure of a scene from a set of 2D views. This requires proper camera calibration to ensure that the 2D images can be used for metric information extraction. Afterwards, the poses of the calibrated camera can be estimated from two images, then the 3D structure can be reconstructed up to an unknown scale factor, which can be found by detecting an object of known size.

{{< hint info >}}
Geometric camera calibration, also known as camera intrinsic calibration or multi-camera calibration, is the process of estimating the parameters required to relate (2D) points in a camera’s image plane with (3D) points in the world scene the camera is viewing. With a calibrated camera, each point on a sensor translates into a ray emanating into the scene, providing directional information about points in a scene.
{{< /hint>}}

With a pair of calibrated cameras (typically called a stereo pair), it is possible to triangulate distances and make estimations of the distance to points in the world. Determining geometry from multiple views (either from multiple cameras or multiple frames from one) is at the heart of classical computer vision.

Generally, camera calibration means the process of computing a camera's physical parameters, like image center, focal length, position and orientation, etc. This is called explicit calibration. Some intermediate parameters can also be calibrated for either making 3D measurements (back-projection) or prediction image coordinates from known world coordinates (projection). This type of calibration is called implicit calibration. A camera's intrinsic parameters do not change if you move the camera, that is what separates the intrinsic parameters from the extrinsic ones. Despite this the intrinsic parameters may change if the lens is adjusted. 

{{< hint important >}}

A camera's intrinsic parameters do not change if you move the camera, that is what separates the intrinsic parameters from the extrinsic ones. Despite this the intrinsic parameters may change if the lens is adjusted. There may be small influences on the intrinsic parameters from moving the camera (as the camera is not perfectly rigid) or from changing surroundings (e.g. temperature), but they are small enough to be disregarded for most use cases.

{{< /hint >}}

## 2 Theoretical Framework

### 2.1. Pinhole Camera Model

Two views can be obtained simultaneously as in a stereo rig, or sequentially, by moving the camera relative to the scene. Both situations are geometrically equivalent, but if two cameras were used simultaneously they would obviously need to be calibrated separately. A camera matrix is a 3x4 matrix which describes the mapping of a pinhole camera from 3D points in the world to 2D points in an image. The main premise of the pinhole camera model is that the camera aperture is described as a single point. Hence, it is based on the principle of co-linearity, where each point in the object space is projected by a straight line through the projection centerinto the image plane.

![21312](https://csundergrad.science.uoit.ca/courses/cv-notes/notebooks/images/camera-geometry.png)

### 2.2. Epipolar Geometry

### 2.3. Essential Matrix

### 2.4. Structure From Motion: 3D Reconstruction

## 3 Experimentation

Multiple cameras were used for further result discussion, but only the cameras that provided best results were closely studied. It is also important to note that despite having applied similar procedures to analyse results, both experiments differ in many ways (ie: camera, objects’ geometries, distance at which objects were captured).


### 3.1. First Camera

In order to calibrate the camera the Camera Calibrator app from MATLAB Computer Vision System Toolbox was used. To ensure that the focal length didn’t affect the intrinsic parameters, an app without automatic focus was installed on the phones that took the pictures.

![123901](https://live.staticflickr.com/65535/53852030294_fdc7abc114_w.jpg)

The photos were then calibrated using the Camera Calibrator app from MATLAB. Results for calibration were:

![123123](https://live.staticflickr.com/65535/53850775922_4d87ff997b_z.jpg)

For this calibration, a mean error of 0.33 pixels was obtained, indicating a good calibration. After the calibration, the CameraParameters variable is saved to a data file.


![1231](https://live.staticflickr.com/65535/53852030259_0b3f5e2c19.jpg)

On this project camera motion was recovered and the 3-D structure of a scene was reconstructed from two images taken with a calibrated camera. With the camera parameters obtained, two photos of the same set of objects were taken with one camera, at different positions. These photos were used to construct a "structure from motion from two views". 

![401349](https://live.staticflickr.com/65535/53852104940_065a09d8c7_z.jpg)

The two images are then compared, in order to find point correspondences between them. In order to detect the features to track, detecMinEigenFeatures is used. This function finds corners using the minimum eigenvalue algorithm. The ’MinQuality’ parameter specifies the minimum accepted quality of corners with a scalar value in the range \[0,1\]. If this parameter is reduced, more points will be detected and they’ll be more uniformly distributed throughout the image. Since the motion of the camera is not very large, the Kanda-Luscas-Tomasi (KLT) feature tracker is suited to establish point correspondences. The detected points (150 strongest corners) are displayed:

![2134](https://live.staticflickr.com/65535/53851927528_5c0b16ebb4_w.jpg)

The features are tracked on image 2 using vision.PointTracker function. The correspondences can be visualized as follows:

![1300](https://live.staticflickr.com/65535/53852104935_437eb5aedf_w.jpg)

The essential matrix can then be computed with the estimateEssentialMatrix function. The function also allows to find the inlier points that meet the epipolar constraint. The epipolar inliers are shown as:

![239193](https://live.staticflickr.com/65535/53852032714_5b49837734_w.jpg)

After this step, the location and orientation of the second camera relative to the first one are computed with the relativeCameraPose. To reconstruct the 3-D locations of matched points, a re-detection on the first image is performed using a lower ’MinQuality’ to get more points. This points are once again tracked into the second image. Estimation of the 3-D locations corresponding to the matched points is performed with the triangulate function, which implements the Direct Linear Transformation (DLT) algorithm. To visualize the location and orientations of the camera, plotCamera function is used. And to visualize the 3-D point cloud, pcshow is used. 

One extra step that can be performed is to fit a sphere to the point cloud. This step is useful if the image contains an object with the shape of a sphere. For our set of images, a ball with know diameter was used for testing. To locate the sphere, MATLAB function pcfitsphere is used. 


![139933](https://live.staticflickr.com/65535/53850775872_338b864c80_c.jpg)

The figure shows that the program was able to successfully locate an object that resembles a sphere, even though the ball isn’t fully defined in the 3-D point cloud. Adding the lateral view of the cloud we can see the objects from the original images have depth in the structure. 

![121](https://live.staticflickr.com/65535/53851669366_ae32349d6f_c.jpg)

Since the radius of the ball is known, it can be used to determine the scale factor of the reconstructed image. With this step, the coordinates of the 3-D points in centimeters can be obtained. This means, a metric reconstruction of the scene is achieved. Final point cloud can be seen as follows:

![210319](https://live.staticflickr.com/65535/53851927518_3cbd04f222_w.jpg)

![21049124](https://live.staticflickr.com/65535/53852030204_0d928da300_z.jpg)

Thus, camera motion was recovered and the 3-D structure of a scene was reconstructed from two images taken with a calibrated camera. The program was able to successfully extract features from the objects and add depth to them, while showing the estimated position of the cameras from where the photos were taken. Some features of the ball were more easily extracted than others. The ball is only partially found on the
point cloud because it is too close to the edge of the frame which distorts.


In MATLAB, one aspect of the program is that it doesn’t give the same exact results 100% of the time. For this test, the program works almost every time, but sometimes the zoom in the last two figures isn’t correct.


### 3.2. Second Camera