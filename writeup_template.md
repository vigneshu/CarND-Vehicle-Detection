## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

** Vehicle Detection Project **

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[no_car]: ./images/no_car.png
[car]: ./images/car_hog.png
[car_hog]: ./images/no_car_hog.png
[no_car_hog]: ./images/car.png
[pipeline_1]: ./images/pipeline_1.png
[pipeline_2]: ./images/pipeline_2.png
[pipeline_3]: ./images/pipeline_3.png
[pipeline_4]: ./images/pipeline_4.png

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook (or in lines # through # of the file called `some_file.py`).  

The code for extracting HOG features is defined in the method get_hog_features and is contained in the cell "Utility functions (from lecture)"

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][car]
![alt text][no_car]

I then explored different color spaces and different `skimage.feature.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.feature.hog()` output looks like.

Here is an example of HOG features of the above two images `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(8, 8)`:


![alt text][car_hog]
![alt text][no_car_hog]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters. I manually  compared the accuracy for different hog parameters. The final configuration of orientation 11, pixels per cell 16 and cells per block 2 gave an accuracy of 98.8%

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

The svc classifier clode is under the cell "SVC classifier". You can see the accuracy score in the cell right below that. I sed hog features(no color histogram). The cell above the classifier extracts hog features from images and splits it into test and train datasets. This is used to train the classifier

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The find_cars funciton from the lesson is used for sliding window search. The function takes pix_per_cell and cell_per_block to calculate the windows. cells_per_step of 2 has been used. Moreover the function also takes ystart and ystop to take a part of the image provided to function. This is suseful because we know that the cars will be in the lower half image(we can ignore sky)

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Here are some example images of pipeline:
First, pass the image to find_cars function. The fuctions slides windows onto image and finds multiple rectangles which could possibly be a car
![alt text][pipeline_1]


Next find the heatmap image for the corresponding images and rectangles pair. Apply number of rectangles threshhold to heatmap image.  The threshhold helps in removing false positives
![alt text][pipeline_2]

Then, get labels from the heatmap image
![alt text][pipeline_3]

Copy these labels onto original image and plot it to get the rectangels over the car
![alt text][pipeline_4]

Increasing pixels_per_cell made considerable difference in the execturion speed. Also, I had tried different scales to resize the image while searching for the cars in an image. I reduced the number of scales to 4. I had 8 before
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./test_video_out.mp4)
Here's a [link to my video result](./project_video_out.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

The images for the heatmap have been included above in the pipeline explanation steps.


---

###Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The main problem was with speed while processing frames. I feel the speed considerably was better after changing pixels_per_cell in find_cars. I feel time SVC takes to predict does not need much improvement but rather the find_cars which slides windows is the area to work on to improve the speed.

Another problem is when I used color histogram. I feel the color histogram can cause problems for black cars, since both the road and car is black and the color features may wrongly weight roads as cars. Therefore, I just stuck with hog features, which mainly looks at shapes

The pipeline could likely fail for distant cars. Having very small scales causes a lot of false positives.

The model can be improved by keeping track of vehicle position frame. The rectangles found in the previous frame can be carried on and the threshhold for heatmap can be increased. This will significantly reduce the false positives. Also, in current implementation the rectangles drawn keep changing in size every frame. Having previous frames could also stabilise this 

