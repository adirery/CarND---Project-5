> Blockquote

# Udacity Self Driving Car Engineer - Project 4

This readme explains the various aspects of Project 4 - developing an algorithm to find car lane lines, calculate curvature radius and offset of the car from the road center and applying the algorithm to a short movie.

# Overview
The goals / steps of this project are the following: 
- Compute the camera calibration matrix and distortion coefﬁcients given a set of chessboard images. 
- Apply a distortion correction to raw images. 
- Apply a perspective transform to rectify binary image ("birds-eye view"). 
- Detect lane pixels and ﬁt to ﬁnd the lane boundary. 
- Determine the curvature of the lane and vehicle position with respect to center. 
- Warp the detected lane boundaries back onto the original image. 
- Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

# Calibrate Camera
The code for this step is contained in the second code cell of the IPython notebook. 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is ﬁxed on the (x, y) plane at z=0, such that the object points are the same for each calibration image. Thus, objp is just a replicated array of coordinates, and objpoints will be appended with a copy of it every time I successfully detect all chessboard corners in a test image. imgpoints will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.

I then used the output objpoints and imgpoints to compute the camera calibration and distortion coefﬁcients using the cv2.calibrateCamera() function. I applied this distortion correction to the test image using the cv2.undistort() function and obtained this result:

# Calculate transformation matrix to warp image "up" & "back"
In this step I use the cv2 getPerspectiveTransform function to define the transformation matrix from a specific set of points (src) to a specific set of points (dst) as below:
Source / Destination:
- 580, 460  --> 320, 0
- 203, 720 --> 320, 720
- 1106, 720 --> 960, 720
- 700, 460 --> 960, 0
This transformation can also be seen in the image below with the marked "box" that is being transformed:

![pre-warp --> post-warp](https://github.com/adirery/CarND---Project-4/blob/master/output_images/lane_marking.png)


# Define image pre-processing pipeline
The image pre-processing pipeline consists of the below steps:
1. Transform image to HLS space
2. Apply the sobel filter on the L-channel
3. Applying a threshold filter to create first binary output
4. Apply threshold filter on S-channel to create second binary output
5. Combine first & second binary output
6. Warp image using the transformation matrix calculated in the previous step 

Below the binary output without warping & binary output incl. warping:
![Pre-processing without warping](https://github.com/adirery/CarND---Project-4/blob/master/output_images/pipeline_nowarp.png)

![Pre-processing incl. warping](https://github.com/adirery/CarND---Project-4/blob/master/output_images/pipeline_warp.png)


# Define lane identification algorithm
For the lane identification I use a sliding window algorithm based on the histogram of the bottom of the image. I added some further processing steps to the "basic" sliding window algorithm to smooth out the lane finding. In this step I also calculate the radius of the curvature and the distance of the car center (camera) from the middle of the lane. 

The lanes are subsequently marked for validation purposes in the jupyter notebook as shown below:
![Lanes marked in lane identification algo](https://github.com/adirery/CarND---Project-4/blob/master/output_images/lane_algorithm.png)

# Define image post-processing pipeline
The post processing is basically warping back the image, specifically the lanes found into the original form and overlaying the original image with the lanes found.
The output of the post-processing is shown below:
![Post-processing result](https://github.com/adirery/CarND---Project-4/blob/master/output_images/post_processing.png)

# Define end-to-end pipeline
The end to end pipeline puts all of the above processing steps together:
1. Pre-process image
2. Find lanes
3. Post-process image

# Run end-to-end pipeline on each frame

The end-to-end pipeline is subsequently run on each frame of the project video:
[Link to output video](https://github.com/adirery/CarND---Project-4/blob/master/project_output.mp4)


# Discussion
This project was again a very interesting project. I liked the fact that we took a problem that we solved in the beginning of the term and applied some learnings of this semester to solve it again, but better. There was a lot of techniques on the image processing side that I was not aware of (e.g. sobel transform).

- Main challenges: The lecture to this project actually was really great. It contained all the individual bits & pieces that made up this project, so while the lecture itself and developing the solutions to the subproblems was challenging, the implementation of the final solution was much simpler. This made me realize again that breaking down a problem into digestable chunks makes the solving much easier. Ultimately there were a couple of points which were tricky, such as implementing the sliding window and optimizing the jumping of the lanes.
- Outlook: I think the algorithm works well on rather straight roads with rather clear lines. As soon as you go into the trickier roads (see the challenge videos) the algorithm fails quite clearly which would be a disaster in a real-life scenario, i.e. the algorithm is not robust. There are many things that could be optimized in this regards - predicting the curvature of the lanes, making the averaging over multiple frames more dynamic, and optimizing the pre-processing pipeline with various techniques (e.g. looking at the R-part of the RGB), making the warping dynamic, etc. I hope there will be a chance to implement some of this in the next semester.