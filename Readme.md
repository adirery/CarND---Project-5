# Udacity Self Driving Car Engineer - Project 5

This readme explains the various aspects of Project 5 - developing an algorithm to find cars in a movie clip

# Overview
The steps of this project are the following: 
- Read training data (cars & no-cars)
- Extract features of a single image for all images, in particular:
	- HOG features
    - Spatial features
    - Color Histogram features
- Split training data into training & validation set
- Train Support Vector Classifier (SVC) on the training set; validate classifier
- Conduct sliding window search on lower half of each frame of the video, in particular:
	- Extract Features for each window
    - Classify window based on features (car / no-car)
    - Create heatmap for each pixel
    - Create final box based on heatmap & threshold

# Feature extraction
For the feature extraction part I used three different methods:
1. The HOG algorithm to calculate histogram oriented gradients from the skimage library was used with the parameters below:
	- orientations: 8. I tried different numbers, but ultimately higher numbers didn't increase the accuracy but increased the calculation time.
	- pixels_per_cell: 8. Again I tried different numbers and settled ultimately with 8 based on "visual feedback"
	- cells_per_block: (2, 2) 
	- transform_sqrt: True 
	- feature_vector: True. I experimented a bit with not returning the feature vector and only calculating HOG for the entire picture (instead of each window) which ultimately reduced calculation time massively, however my implementation was not correct somehow and after a lot of debugging I decided to go back to the brute-force method.

2. As a second set of features I use spatial binning. Here I resize the image to smaller dimensions using the openCV function resize and then return a single dimensional feature vector from this array using the ravel function. 

3. As a third set of features I calculate the histogram of the different color channels (in the final version it is RGB).

# Read training data & split into training and validation set with random shuffling
In this step I read in the training set provided and split it into training set (80%) and validation set (20%) after shuffling it. 

# Train classifier & save
Once I have the training set & the features extraction algorithm I combine everything and train the SVC with the following steps:
1. Load cars & no-cars
2. Extract features for both cars & no-cars
3. Normalize features with StandardScaler
4. Split training set (80%) & validation set (20%)
5. Train LinearSVC
6. Test accuracy of SVC with validation set
7. Save SVC & related attributes to pickle file

# Conduct sliding window search
1. Go through the lower half of the image (360px -> 720 px)
2. Each window is 96px & 96px and 120px & 120px
3. For each window I extract the HOG, spatial binning and histogram features
4. I normalize the features
5. Then I classify each window (car or no-car)
6. Based on the classification I create a heatmap of each pixel (one window = +1)
7. I filter the heatmap with a threshold (2+ heat required to be in final recognition)
8. As a last step I draw the box on the image

The result on an individual image is below:
![Sliding window on single image](https://github.com/adirery/CarND---Project-5/blob/master/SVC_Example.png)

The end-to-end pipeline is subsequently run on each frame of the project video:
[Link to output video](https://github.com/adirery/CarND---Project-5/blob/master/project_output.mp4)


# Discussion
Again I found this project to be a very interesting and challenging one. The implementation part of this project I found not so difficult, as the lectures gave a very good guidance and provided all the building blocks for the final processing pipeline. I found however the trial&error for the parameters more challenging, especially since for each trial&error I had to first retrain the classifier before trying out on the final video. There are a number of things that could be improved in my implementation if time would permit:
- Generate additional training data (e.g. flipping, turning, ...) 
- Extract more features (e.g. hough)
- Further tune parameters of hog
- Use another classification algorithm, e.g. neural network
- Smoothen classiciation with a FIFO que or similar over multiple frames
