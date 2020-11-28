---
title:  "Ch2: ROS subscribing poses of objects in Unreal"
date:   2020-11-27 15:04:23
categories: [Airsim]
tags: [Airsim]
---
In the previous tutorial, we looked into how to generate a custom unreal environment and spawn an character which moves along a spline curve. 

Now we move on to associate the ROS with Unreal environment we created. For that, we use [airsim_ros_pkgs](https://github.com/icsl-Jeon/airsim_ros_pkgs) which is a custom forked version from the original package to include object pose extraction. 

## 1. Setup

### Basic dependenices 

* **mavros_msgs** 

```
sudo apt-get install ros-melodic-mavros-msgs  
```

* **gcc 8.0** 

```
 sudo apt install gcc-8 g++-8
```

* Building  airsim_ros_pkgs

```
cd ~/catkin_ws/src/AirSim/ros/src
rm -r airsim_ros_pkgs
git clone https://github.com/icsl-Jeon/airsim_ros_pkgs.git
cd ~/catkin_ws
catkin build airsim_tutorial_pkgs -DCMAKE_C_COMPILER=gcc-8 -DCMAKE_CXX_COMPILER=g++-8
```

### Configuring the airsim vehicle under PX4 

Download an example `setting.json` from my [drive](https://drive.google.com/file/d/1O5jBOMDusBs9tLmFTPET7cEZeFa1wUUE/view?usp=sharing). Move the file into `~/Documents/AirSim` and replace the original one (you mush have the folder if you had followed the chapter 1 posting). 


## 2. Simulation 

We now do airsim simulation. From here, you will need to launch my `airsim_ros_pkgs`. 

### Spawn PX4 vehicle drone in unreal 




