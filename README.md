# Robust_Lane Detection

Goal:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

Process:
1. Calibration / Undistortion

2. Grad/Color Threshold
3. Perspective Transformation
4. Lane Detection
5. Radius of Curvature and position of vehicle



[image]: ./output_images/calibration.png
[image1]: ./output_images/threshold_image.png

[image2]: ./output_images/transformed.png
[image3]: ./output_images/lane_detection.png
[image4]: ./output_images/final.png


### 1. Calibration / Undistortion

I have used calibration images of chessboards in order to identify its corners and compared with their objective points to calculate calibration. I used cv2 calibratecamera function to derive mitx and dist from the img and corner points. Then based on mitx and dist from calibration, I can undistort images.
![alt text][image]

### 2. Gradient / Color Threshold

I have applied 1) absolute gradient threshold (line 143) or 2) magnitude and directional gradient threshold to grayscaled image (line 160-186). While with 1) abs gradient threshold, it filters for gradient with respect to x and y axis, 2) magntiude threshold measures the size of gradient and directional threshold complements by adding directional filter. By setting 'or' for combinations of gradient threshold, we can filter for gradient of interest with certain abs of gradients in x and y direction or gradient with size and direction of interest.

Then I have added a color filter based on HLS of image. After converting RGB to HLS space, to remove variations by shadow and light conditions, I have selected s of the image and filtered. (line 259-266)

![alt text][image1]

### 3. Perspective Transformation

Perspective Transformation was done through a function called 'perspectivetransform' (line 310). Based on points of source that I specified and destination to be displaced, it transforms the source image using cv2 getPerspectiveTransform, warpPerspective functions.

I verified that my perspective transform was working as expected by drawing the src and dst points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.
![alt text][image2]

### 4. Lane Detection
From transformed images, I have firstly took half bottom of image and set base x-pixel location for left and right lane based on its histogram. Then, I have broken the images into a number of windows and took mean of nonzero pixels for each of the windows. Then based on set of mean values, I have fit polynomial function for left and right side of lane. (line 435) 

![alt text][image3]

### 5. Radius of Curvature and position of vehicle
Based on inverse of polynomial functions I derived, I can get x values for range of y. Then, I would covert these pixels into meters by multiplying x and y with 3.7/700 and 30/720 meters per pixel. Afterwards, I refit to get coefficients of polynomial now in terms of meters and calculated a radius of curvature. Further explanations on mathematical background behind this formula can be found at [here](https://en.wikipedia.org/wiki/Radius_of_curvature). (line 386)

In order to identify position of vehicle, I have used min and max of x-pixel from unwarped image and took average of it and compared with the center of image. After taking the difference, I have multiplied with meter per x-pixel to derive its position. (line 598).

### Output

![alt text][image4]
