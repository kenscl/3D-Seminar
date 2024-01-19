# Introduction 
## Kimera 
- Library for real time metric semantic localisation and mapping
## Motivation 
- Providing a Library for real time... for other researchers 
- combining VIO and slam
- Merging VIO, PGO, mesh reconstruction and semantic segmentation
- perform in real time on only a CPU and with the use of stereo Cameras


# What does Kimera do? 
- estimate the pose and optimize it using PGO
- generate two maps a multi frame map for low level tasks like obstacle avoidance (Kimera-Mesher) and a metric semantic map that is more accurate (Kimera-Semantics)
- semantic annotation of the 3D mesh

# The structure of Kimera
- Split into four parts (some of them are split into different parts as well) and four threads 
- Kimera-VIO frontend is thread one, thread two is Kimera-VIO backend and Kimera Mesher
- RPGO and Semantics are on slower threads

# Kimera-VIO
- Visual inertial state estimation at IMU rate
- Split into front and backend 
- frontend processes the sensor data while the backend produces state estimates
## Frontend 
- splint into two parts: IMU and Vision frontend
### Imu-Frontend
- IMU frontend is only does IMU preintegration 
- preintegration is the process of using accelerometer and gyroscope data and integrating it to get position and angle information
### Vision-Frontend
- The Vision frontend is more complex 
- It tracks the movement of important features
- This is done by first detecting the features and then tracking them 
- detection is done using the Shi-Tomashi corner detection algorithm 
- tracking is done using the Lukas-Cannade tracker  tracks the movement of important features
- This is done by first detecting the features and then tracking them 
- detection is done using the Shi-Tomashi corner detection algorithm 
- tracking is done using the Lukas-Cannade tracker 
- geometric verification using RANSAC

### Vision-Backend
- Use of fixed lag smoothing to estimate the position
- in our case the fixed lag smoother is a factor graph consisting of IMU and visual measurements that gets solved using iSAM2
- todo
# Kimera-RPGO
- Detects loop closures and solves them using Pose graph optimisations
## Detection
- Loop closures denote the revisiting of an area
- loop closures are detected by a bag of words approach 
- this means that visual features are put into different categories and counted for each picture
- if two of these representations are similar enough -> loop closure
- this is again verified using RANSAC though this can still include spurious loop closures
## Correction 
- Use of PCM to find the maximum set of possible consistent trajectories
- Filtering of the trajectories for outliers using 1. odometry (error should be smaller than noise)
and 2. by compering the current loop closure to previous ones. 
- 2. Is done by comparing the new loop closure to an adjacency Matrix. Each time a loop closure passes a new column and row get added to the adjacency Matrix.
- Finally a nother maximum Clique problem is solved over thees trajectories and then filtered with Gauss-Newton before being added to the pose graph

# Kimera-Mesher
- creates two kinds of meshes: 1. a Perframe mesh and 2. a Multiframe mesh
- Multiframe mesh is constructed from perframe mesh
## perframe mesh
- created by 2D Delauny triangulation of the landmarks selected by Kimera-VIO 
- This is then projected onto the 3D point estimations of the Backend 
- usually ment for low level tasks like obstacle avoidance
## Multiframe mesh
- The vertecies of the per frame mesh not in the multiframe mesh are added to the mesh
- The positions of all vertecies are then updated with new VIO positions
- Vertices that fall out of the VIO time horizon are marginalized out
- If plare surfaces are detected regularity factors get added to smooth out the mesh
