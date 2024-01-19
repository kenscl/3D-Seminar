# Introduction 
## Kimera 
- Library for real time metric semantic localisation and mapping
## Motivation 
- Providing a Library for real time... for other reasearchers 
- combining VIO and slam
- Merging VIO, PGO, mesh reconstruction and semantic segmentation
- perform in real time on only a CPU and with the use of stereo Cameras


# What does Kimera do? 
- estimate the pose and optimize it using PGO
- generate two maps a multi frame map for low level taskss like obsticle avoidance (Kimera-mesher) and a metric semantic map that is more acurate (Kimera-Semantics)
- semantic annotaion of the 3D mesh

# The structure of Kimera
- Split into four parts (some of them are splite into differnet parts aswell) and four threads 
- Kimera-VIO frontend is thread one, thread two is Kimera-VIO backend and Kimera Mesher
- RPGO and Semantics are on slower threads

# Kimera-VIO
- Visual inertial state estimateion at Imu rate
- Split into front and backend 
- frontend processes the sensor data while the backend produces state estimates
# Frontend 

