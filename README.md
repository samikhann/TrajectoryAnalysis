# TrajectoryAnalysis
University Project

This project aims to predict the path and the final destination of a pedestrian after a time frame of 4.8 secs.

###Data Set:

x, y: the position of the person in pixel space, where (0, 0) is the center of the frame.

frame_number: the time (frames)at which the position was tracked

gaze_direction: the viewing direction of the person in degrees (0 degrees means the person is looking upwards)


##The project is divided into 4 parts as shown below:

###Feature Extraction

(a) Pedestrians’ speed (Newtonian) at each time-steps (at 2.5 fps or sampled every 0.4 seconds)

To get the speed for each pedestrian at each timestep
    param:
        agent_list: array of all agents' ids ("agent_id") in the dataset
        input_xy: array of all x and y coordinates in the dataset
    
    returns:
            speed_list: array of speeds for each pedestrian at each timestep in the dataset


(b): Euclidean distance (and/or direction vector) from the destination points (if not given)

To get distance (eucledian) of each pedestrian from the destination points
param:
    input_xy: array of all x and y coordinates in the dataset
    destination_points: array of destination point coordinates (x, y)

returns:
        destination_distance_list: array of distance to the destination points. shape (len(input_xy), 4). 
        each row contains: 
        (distance_to_destination1, distance_to_destination2, distance_to_destination3, distance_to_destination4) 


(c) Euclidean distance from the nearest pedestrian (neighbor). Here, a neighbor pedestrian can be defined as a pedestrian who’s present in the scene at the same time-step.

To get nearest pedestrian and the distance from nearest pedestrian (neighbor)
    param:
        agent_list: array of all agents' ids ("agent_id") in the dataset
        input_xy: array of all x and y coordinates in the dataset
        frame_number:array of all frames ("frame_id") in the datset
    
    returns:
            neighbor_dist_list: array with the distance from nearest pedestrian for each individual
            neighbor_id_list: array of ids of all nearest pedestrians for each pedestrian in the dataset


### Data Preprocessing

Our target is to give the machine learning model 8 frames taken at a speed of 2.5 fps (3.2 secs) and get the prediction for the next 12 frames (4.8 secs). In order to acheive this goal we extracted a set of features from the frames including the position of each pedestrian in the frame, their speed, their destination and their nearest neighbor.
We started the preprocessing by creating a dataframe containig a row for each pedestrian in each frame with all the previous features for each, then we added the position and the speed of the nearest neighbor to each row. Now after we have the data organized we need to get the inputs and the outputs of the model which are 8 frames for the input and 12 frames for the output.
We sorted the data by the frame id then we started a loop over each unique pedestrian to extract the frames where this pedestrian was present. Because the inputs are 8 frmaes and the output is 12 frames with 2 values for the position in each frame we extracted a window of 20 frames split into inputs and outputs for each pedestrian and moved this window frame by frame till we use all the frames of this pedestrian for example:
if we have 25 frames for this pedestrian this means we have 3 windows in this way 1:20,2:21,3:22 and so on. Now we have the inputs as a list of windows of 8 frames and the outputs as the next 12, we split this data into 75% for training and 25% for testing. Now the preprocessing is all set.

### Machine Learning Model

The problem we have is a multi output regression problem where we have to predict a multiple continous values. In order to do this we used Multi Output Regression function in SKLearn library which generates multiple variation of the model we are using trained for each value of the outputs. We tried 2 models to perform this task explained below:

#### Ridge Regression
Ridge regression is a model used to analyze any data that has multicollinearity and uses L2 regularization to tackle this problem. When there is a problem with multicollinearity, least-squares are unbiased, and variances are enormous, resulting in projected values that are distant from the actual values.
This model performed well with Mean Squared error of 0.54 and Coefficient of Determination (R^2) of 0.95

#### XGBoost
XGBoost is a popular and efficient open-source gradient boosted trees solution. Gradient boosting is a supervised learning approach that combines the estimates of a collection of smaller, weaker models, Descision Trees in this case to attempt to accurately predict a target variable.
This model performed even better with Mean Squared error of 0.21 and Coefficient of Determination (R^2) of 0.98

### Calculate Metrics

We calculated 3 metrics for each model explained below:

#### ADE

This stands for Average Displacement Error which means the average eculedian distance between all the predicted frames values and the actual values.
The ADE for the XGBoost is 0.44
The ADE for the Ridge Regression is 0.69

#### FDE

This stands for Final Displacement Error which means the average eculedian distance between the last predicted frame values of each window and its actual values.
The FDE for the XGBoost is 0.64
The FDE for the Ridge Regression is 1.3

#### Near Collision Percentage

This is the percentage of near-collisions in the model predicted trajectories of the pedestrians. A near-collision takes place when the Euclidean distance between the pedestrians is below 0.1 meters.
The percentage for the XGBoost is 0.37%
The percentage for the Ridge Regression is 0.42%

### Conclusion

In conclusion, we were able to perform trajectory some how accurately and the XGBoost was the superior model here as it outperformed the Ridge Regression in every single metric.

### Run To run this project, follow these steps:

####Run with Jupyter:

1. Open this project in any python editor (Ex: PyCharm)
2. Then, open the terminal in PyCharm
3. And run the command
	a. Pip install torch
	b. Pip install jupyter
4. If both packages successfully installed, then you can run this command   on PyCharm terminal
	a. Jupyter notebook
5. It will run on the browser. 

####Run with Google Collab:

Here, you don’t have to install any libraries. It is already there, and we can use it.

1. So, you can import project and can run “Open_Traj_final.ipynb” file.
2. If you don’t want to run whole project, then here you can import few files:
	a. file2.csv
	b. transformed_data.csv
	c. Open_Traj_final.ipynb
