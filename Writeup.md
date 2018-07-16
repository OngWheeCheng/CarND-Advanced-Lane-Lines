# **Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./md_images/camera_undistorted.png "Camera Distorion Corrected"
[image2]: ./md_images/example_undistorted.png "Example Distortion Corrected"
[image3]: ./md_images/example_color_transform.png "Example Color Transform"
[image4]: ./md_images/example_warp.png "Example Warp"
[image5]: ./md_images/example_laneline_pixels.png "Example Lane Line Pixels"
[image6]: ./md_images/example_lane_area.png "Example Lane Area"

### Camera Calibration

The code for this step is contained in the Camera Calibration cell of the IPython notebook, _AdvancedLaneLines.ipynb_.

An "object points" is first prepared, which will be the (x, y, z) coordinates of the chessboard corners in the world. Assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objPoints` will be appended with a copy of it every time all chessboard corners in a test image are detected.  `imgPoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

The output `objPoints` and `imgPoints` are used to compute the camera calibration and distortion coefficients using `cv2.calibrateCamera()`.  This distortion correction is applied to the test images using `cv2.undistort()` and an example result is shown below: 

![alt text][image1]

### Pipeline (single images)

#### 1. An example of a distortion-corrected image.

To obtain distortion correction images, the camera calibration and distortion coefficients computed in the Camera Calibration section (above) are applied to the test images using `cv2.undistort()` and an example result is shown below:

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

A combination of color and gradient thresholds were applied to the distortion-corrected image to obtain a thresholded binary image (refer to Color/Gradient Threshold cell). The image is converted to HLS and LUV color spaces to obtain the S channel and L channel respectively before applying color thresholding. Likewise, the image is converted to grayscale before applying Sobel x operator and thresholding for edge detection. An example of the combined binary image is depicted below.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

To obtain the bird’s eye view of an image, a perspective transform matrix is applied to the image using `warpImage()` (refer to Perspective Transform cell). This perspective transform matrix is obtained using `cv2.getPerspectiveTransform()` with the following 4 pairs of source and corresponding destination points: 

| Source Points (x,y) | Destination Points (x,y)| 
| :-: | :-: |
| 240, 720 | 320, 720 | 
| 580, 460 | 320, 0      |
| 720, 460 | 960, 0      |
| 1120, 720 | 960, 720        |

The correct working of perspective transform was verified by drawing the source and destination points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image as shown below:

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The lane-line pixels are identified using Udacity-provided algorithm (refer to Find Lane Lines cell). An example of the lane-line image is shown below:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The radius of curvature of the lane is calculated using Udacity-provided algorithm (refer to Find Curvature cell). 

The position of the vehicle with respect to center is determined by determining the offset from the center of the lane lines (i.e assume camera is mounted at the center of the car => center of vehicle). An example of the radius of curvature of the lane is shown below:

![alt text][image6]

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's the [link](https://www.dropbox.com/s/vxyif7k7p24w83y/CarND-Advanced-Lane-Lines.mp4?dl=0) to the video.

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I did not implement sanity check on the lane lines, and hence, there are some instances where the radius of curvature of the lane does not make sense.

The pipeline may fail in low light or bad weather conditions, e.g. night, snow, etc. 