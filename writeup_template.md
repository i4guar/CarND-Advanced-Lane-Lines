## Writeup Advanced Lane Lines

[Link to my jupyter notebook containing my code](./advLaneLines.ipynb)
---

**Advanced Lane Finding Project**

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
[image1]: ./output_images/camera_calibration.jpg "Calibration"
[image2]: ./output_images/calibration_real_world.jpg "Calibration real world"
[image3]: ./output_images/thresholded_binary.jpg "Thresholded Binary"
[image4]: ./output_images/birds_eye_test_1.jpg "Warp Example Straight"
[image5]: ./output_images/birds_eye_test2.jpg "Warp Example Curved"
[image6]: ./output_images/identified_lane.jpg "Identified lane"
[image7]: ./output_images/lane_bin_bird_eye.jpg "Binary lane bird eye"
[image8]: ./output_images/pipeline_test_0.jpg "Output 1"
[image9]: ./output_images/pipeline_test_1.jpg "Output 2"
[image10]: ./output_images/pipeline_test_2.jpg "Output 3"
[image11]: ./output_images/pipeline_test_3.jpg "Output 4"
[image12]: ./output_images/pipeline_test_4.jpg "Output 5"
[image13]: ./output_images/pipeline_test_5.jpg "Output 6"
[image14]: ./output_images/pipeline_test_6.jpg "Output 7"
[image15]: ./output_images/pipeline_test_7.jpg "Output 8"
[video1]: ./project_video_output.mp4 "Video"

## Rubric Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.

This document!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second and third code cell of the IPython notebook located in ["./advLaneLines.ipynb"](./advLaneLines.ipynb).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (test images)

#### 1. Provide an example of a distortion-corrected image.
*Jupyter Notbook Codeblock #4.*

Here is an example. Small corrections can be detected especially at the edge of the image:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
*Jupyter Notbook Codeblock #5.*

I used a combination of color and gradient thresholds to generate a binary image. Specifically, I used the sobel operator in the x direction with a threshold and the thresholded saturation value of the HSL colorspace. Then I combined these two with an "OR" operator.

Here's an example of my output for this step.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.
*Jupyter Notbook Codeblock #6.*

 I chose the hardcode the source(`src`) and destination(`dst`) points with following points:

| Source       | Destination   | 
|:-------------:|:-------------:| 
| 250, 690      | 250, 720        | 
| 1050, 690      | 1000, 720      |
| 597, 450     | 250, 0      |
| 686, 450      | 1000, 0        |

The code for my perspective transform includes a function called `warp()`. The `warp()` function takes as inputs an image (`img`) and warps the image with the perspective transform matrix gained by using hardcoded `src` and `dst` points.

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image with straight lane lines and its warped counterpart to verify that the lines appear parallel in the warped image.

Here is an example for an image with straight lane lines:
![alt text][image4]

Here is an example for an image with slightly curved lane lines:
![alt text][image5]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?
*Jupyter Notbook Codeblock #7.*

I started out by warping the thresholded binary image into birds eye view. Then I took the bottom half of the image and applied a histogram showing distribution of the number of white pixels(likely lane line pixels) across the x axis. The maximum of the left half of the histogram is assumed to be the starting point of the left lane and the maximum of the right half of the histogram the starting point of the right lane.

Starting there, I applied the sliding window algorithm. I searched for potential pixels within a window around these points and assigned them to be the lane lines. Sliding the window up with each iteration. If a threshold of a number of pixels was reached the next window was recentered to be at an new x position (the average of the lane pixels of the previous window).

After having identified all lane line pixels I fitted a 2nd order polynomial through each of the lines.

The following images show my results:

![alt text][image7]
![alt text][image6]


#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.
*Jupyter Notbook Codeblock #9.*



Using  
<img src="https://render.githubusercontent.com/render/math?math=R_{curve}=\frac{(1+(2Ay %2B B)^2)^{3/2}}{|2A|}"> 

with the polynomial

<img src="https://render.githubusercontent.com/render/math?math=f(y) = Ay^2 %2B By %2B C"> 

I calculated the curvature of each lane line and returned the average.

The offset was calculated by subtracting the image center, which is also the car center from the lane center which is between the two points where the polynomials start.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.
*Jupyter Notbook Codeblock #11.*

Using all the code above and putting them into a pipeline, I can detect car lanes. Here are the results for all test images:

![alt text][image8]
![alt text][image9]
![alt text][image10]
![alt text][image11]
![alt text][image12]
![alt text][image13]
![alt text][image14]
![alt text][image15]


---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).
*Jupyter Notbook Codeblock #13, 14.*

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

For very curved lanes and lanes which are harder to detect and tolerating smaller thersholds, this algorithm has a hard time. I could be improved by using the history of the lane lines detected and by spending more time adjusting the thresholds to be more robust.
My lane is still a little jittery, which could be improved by averaging the history of the detected lane lines.

Starting with the simple approach using sliding windows my result was suprisingly good. However, after using the polynomial and search around the previously detected line, my result got worse, so I returned to my original solution. I probably did not spend enough time adjusting my whole pipeline to be optimized to search around the fitted polynomial, so my algorithm did not improve by using my previous detected line.