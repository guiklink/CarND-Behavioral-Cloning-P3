# **Behavioral Cloning** 
---

The goals / steps of this project are the following:
* Use the simulator to collect data of good driving behavior
* Build, a convolution neural network in Keras that predicts steering angles from images
* Train and validate the model with a training and validation set
* Test that the model successfully drives around track one without leaving the road

[//]: # (Image References)

[image1]: ./examples/placeholder.png "Model Visualization"
[image2]: ./examples/placeholder.png "Grayscaling"
[image3]: ./examples/placeholder_small.png "Recovery Image"
[image4]: ./examples/placeholder_small.png "Recovery Image"
[image5]: ./examples/placeholder_small.png "Recovery Image"
[image6]: ./examples/placeholder_small.png "Normal Image"
[image7]: ./examples/placeholder_small.png "Flipped Image"

## Rubric Points
###Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/432/view) individually and describe how I addressed each point in my implementation.  

---
###Files Submitted & Code Quality

### 1. Intro

My project includes the following files:
* **Intro.ipynb**: this notebook contains the step by step of my aproach, showing the manipulation of the images, enhancement of the data and three learning models
* **NN_model**: this notebook was used to train the model of my choice. Here the pipeline loads all the data in the memory at once which is faster for running the trainning epochs, however it may require a lot of GPU memory.
* **NN_generator**: this notebook can also train a model. It uses a data generator with an adjustable batch size, making it viable without a lot of GPU power or for huge datasets but it is considerably slower.  
* **drive.py**: for driving the car in autonomous mode
* **model.h5**: contains a trained convolution neural network 

### Model Architecture and Training Strategy

In the notebook [Intro.ipynb](Intro.ipynb) I started by trying a linear NN and the [LeNet NN](). However my design of choice was based on the design used by NVIDIA self-driving cars team and can be seen bellow.

| Layer         	               | 
|:--------------------------------:|
| Input: 160x320x3 RGB Image       |
| Normalization                    |
| Image Crop - Get area of interest|
| Convolution 3@66x200 - Kernel 5x5|
| Convolution 24@31x98 - Kernel 5x5|
| Convolution 36@14x47 - Kernel 5x5|
| Convolution 48@5x22  - Kernel 3x3|
| Convolution 64@3x20  - Kernel 3x3|
| Dropout 50%                      |
| Convolution 64@1x18  - Kernel 3x3|
| Dropout 50%                      |
| Fully Connected - 100 neurons    |
| Fully Connected - 50 neurons     |
| Fully Connected - 10 neurons     |
| Fully Connected - 1 neurons      |
| Output: Float                    |


### 2. Data

The data was collected by recording various laps using a Unit 3D simulator showed bellow:
![alt text][sim]

The car is driven in a bird view perspective, still tha images are captured by three cameras mounted on the car.
![alt text][sim_left] ![alt text][sim_center] ![alt text][sim_right]

In adition for each frame is also captured the turning angle, speed and braking values of the car.

To enhance the amount of data available and improve generalizatrion, for each image I also added a mirror (flipped) image of itself.
![alt text][sim_normal] ![alt text][sim_mirror]


### 3. Attempts to reduce overfitting in the model

When I was training the model it was very clear that not overfitting was extremelly important for a good model. Imagine that after driving a bunch of laps you accidentaly turn the car a little rougher and make a bad turn, without dropout layers the bad maneuver will be broadcasted inside the network causing undesireble effects in the autonomous driving as it overfits to that. Two dropout layers between the last 2 convolutions were used for that. Another thing that I noticed is that if I use a dropout for every convolution, the car will drive extremelly well for the most of the track but will perform badly on the sharp curves. The reson for that I suppose is due to the fact that most of the track is composed by soft turns, therefore not providing as much data for sharp turns as it provides for the soft ones.

### 4. Recovering from the corners

It was important to teach the car that was a good idea to try to stay centered on the road, the intuitive method for that is to drive as centered as possible when recording the drive data. Still how is the car supposed to fix its position if by chance it ended up aproaching the corners? The solution was record many examples where I would only record going from the corner of the road to the center as shown in the schematic bellow.

![alt text][sim_left] ![alt text][sim_recovering]


#### 5. Model parameter tuning

The model used an adam optimizer, so the learning rate was not tuned manually (model.py line 25).

#### 6. Intro Notebook
For more information and details of my procedures check the notebook [Intro]().