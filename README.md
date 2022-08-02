# open-source-SLAM-package
A open-source SLAM package FOR CUHK UAS fundamental group

## Menu
- [open-source-SLAM-package](#open-source-slam-package)
  * [Introduction](#introduction)
  * [Overall Architecture](#overall-architecture)
  * [Summary Table](#summary-table)
  * [Setup Guide](#setup-guide)
    + [Dependency](#dependency)
    + [Compilation](#compilation)
  * [Quick Test](#quick-test)
  * [Updates](#updates)
  * [TODO](#todo)
  * [Related works](#related-works)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>


## 1. Introduction

![image](https://user-images.githubusercontent.com/58619142/181909310-37a21609-c84e-4939-9f0b-bd07619eb319.png)


This package presents a set of state-of-art open-sourced lidar-based Simultaneous  Localization and Mapping(SLAM) software for UAV localization in a 3D factory environment, targeting for:
* Lightweight(both hardware and software);
* Robust;
* High precision;
* integration with other algorithms(navigation, task planning and so on).

Up to now, we have collected and tested with four kinds of lidar SLAM, including pure lidar SLAM algorithms and lidar-inertial SLAM algorithms. Integration of autonomous UAV software and development of low-cost SLAM algorithm will be our future work directions.

## 2. Overall Architecture

<div align="center">
<img src="https://user-images.githubusercontent.com/58619142/181913836-e9d73f88-0804-47b0-bddc-5d68426b37e8.png"
     width="70%"
     alt="Autonomous UAV system overview"/>
<br>
Autonomous UAV system overview
</div>

<br/>

The SLAM module plays a role in perceiving and analyzing environment information gathered by navigation sensors. In a GPS-deny environment, Unmanned vehicles depend on the SLAM algorithm to get accuracy localization information. In general, SLAM algorithms can be divided into lidar SLAM and visual SLAM. The visual SLAM algorithm is low-cost and lightweight, which benefits from camera sensors. However, visual SLAM suffers mostly from illumination variation and motion distortion. The lidar SLAM methods, by contrast, have a stable performance in challanging environment. This is one important reason why engineers prefer lidar SLAM whether in autonomous driving or service robots. 

<br/>

In this project, we take more concern on the lidar or lidar-inertial fusion SLAM algorithm. The lidar SLAM mostly contains below sub-modules:
* offline calibration
* Point cloud preprocessing & sensor data preprocessing
* motion distortion compensation
* Front-end lidar odometry or lidar-inertial odometry
* Back-end optimization 
* Visualization

<div align="center">
<img src="https://user-images.githubusercontent.com/58619142/181915418-cd55e434-fdb4-4f30-98fb-386c2b324990.PNG"
     width="70%"
     alt="Lidar-inertial SLAM system pipeline"/>
<br>
Lidar-inertial SLAM system pipeline
</div>

<br/>

## 3. Summary Table

Here is a summary table for four lidar SLAM methods in our package. As shown in this table, there are two pure lidar SLAM methods(A-LOAM and F-LOAM) and two lidar-inertial SLAM methods(LIO-SAM and Fast-LIO2). 

| SLAM method | Lidar | IMU  | Front-end odometry | Back-end optimization | Loop-closure | feature | Processor |
| ----------- | ----- | ---- | ------------------ | --------------------- | ------------ | ------- | --------- |
| A-LOAM      |  &#10004;    | 	&#10006; | Edge-planar | &#10006; | &#10006; | benchmark | Intel |
| F-LOAM | &#10004; | &#10006; | Edge-planar | &#10006; | &#10006; | Faster than loam | Intel |
| LIO-SAM | &#10004; | &#10004; | Edge-planar | Factor graph optimization | &#10004; | Can handle aggressive rotation | Intel |
| Fast-LIO2 | &#10004; | &#10004; | direct | iterated Kalman filter | &#10006; | Lightweight | Intel/ARM |

<div align="center">
Table 1: A comparision of different SLAM methods in this package 
</div>

## 4. Setup Guide

Before starting the test, we strongly recommend that users follow the following steps to configure the computer environment. We have tested these steps under **Ubuntu 64-bit 18.04**.

### 4.1 Dependency

#### 4.1.1 General Dependency

1. ROS
ROS Melodic:  [ROS Installation](http://wiki.ros.org/ROS/Installation)
2. Eigen
Eigen  >= 3.3.4, Follow [Eigen Installation](http://eigen.tuxfamily.org/index.php?title=Main_Page).

#### 4.1.2 A-LOAM & F-LOAM

1. Ceres Solver
Follow [Ceres Installation](http://ceres-solver.org/installation.html).
2. PCL
Follow [PCL Installation](http://www.pointclouds.org/downloads/linux.html).
3. Trajectory visualization
For visualization purpose, this package uses hector trajectory sever, you may install the package by 
```
sudo apt-get install ros-melodic-hector-trajectory-server
```
Alternatively, you may remove the hector trajectory server node if trajectory visualization is not needed.

#### 4.1.3 LIO-SAM

1. ROS dependency

Open a new terminal and type the following command：
  ```bash
  sudo apt-get install -y ros-melodic-navigation
  sudo apt-get install -y ros-melodic-robot-localization
  sudo apt-get install -y ros-melodic-robot-state-publisher
  ```
  
 2. [GTSAM](https://gtsam.org/get_started/) (Georgia Tech Smoothing and Mapping library)

Again, type the following command:
  ```bash
  sudo add-apt-repository ppa:borglab/gtsam-release-4.0
  sudo apt install libgtsam-dev libgtsam-unstable-dev
  ```
  
  #### 4.1.4 Fast-LIO2
  
  1. livox_ros_driver
  Follow [livox_ros_driver Installation](https://github.com/Livox-SDK/livox_ros_driver).
  
  *Remarks:*
- Since the FAST-LIO must support Livox serials LiDAR firstly, so the **livox_ros_driver** must be installed and **sourced** before run any FAST-LIO luanch file.
- How to source? The easiest way is add the line ``` source $Licox_ros_driver_dir$/devel/setup.bash ``` to the end of file ``` ~/.bashrc ```, where ``` $Licox_ros_driver_dir$ ``` is the directory of the livox ros driver workspace (should be the ``` ws_livox ``` directory if you completely followed the livox official document).

  
### 4.2 Compilation

Firstly, you need to create a ros workspace and initial it. 
Open a new terminal and type the following command：
  ```bash
  mkdir 3d_SLAM_ws && cd 3d_SLAM_ws
  mkdir src && cd src
  catkin_init_workspace
  cd .. && catkin_make
  ```

#### 4.2.1 download A-LOAM and build it 
Clone the repository and catkin_make:
```bash
    cd ~/3d_SLAM_ws/src
    git clone https://github.com/CUHK-UAS-FUNDAMENTAL/A-LOAM.git -b dev_v1.0
    cd ../
    catkin_make
```

#### 4.2.2 download F-LOAM and build it 
Clone the repository and catkin_make:
```bash
    cd ~/3d_SLAM_ws/src
    git clone -b gzx_dev https://github.com/CUHK-UAS-FUNDAMENTAL/F-loam.git 
    cd ..
    catkin_make
```

#### 4.2.3 download LIO-SAM and build it 
Clone the repository and catkin_make:
```bash
 cd ~/3d_SLAM_ws/src
 git clone -b gzx_dev https://github.com/CUHK-UAS-FUNDAMENTAL/LIO_SAM.git
 cd ..
 catkin_make
```

#### 4.2.4 download Fast-LIO and build it 
Clone the repository and catkin_make:
```bash
   cd ~/3d_SLAM_ws/src
    git clone https://github.com/hku-mars/FAST_LIO.git
    cd FAST_LIO
    git submodule update --init
    cd ../..
    catkin_make
    source devel/setup.bash
```
- Remember to source the livox_ros_driver before build (follow 1.3 **livox_ros_driver**)
- If you want to use a custom build of PCL, add the following line to ~/.bashrc
```export PCL_ROOT={CUSTOM_PCL_PATH}```

## 5. Quick Test

### 5.1 Test with PX4 Fundmental  Simulator

The PX4 Fundmental  Simulator is a UAV simulator developed by [Chen yizhou](https://github.com/JINXER000). If you are not  familiar with this simulator, I recommend that you get start by  following this [wiki](https://github.com/JINXER000/FundamentalSimulatorPX4/wiki).  The intergration of algorithms in simulator will be based on [here](https://github.com/CUHK-UAS-FUNDAMENTAL/FundamentalSimulatorPX4/tree/xtdrone/gzx_dev).


![simulator environment](https://user-images.githubusercontent.com/58619142/182297052-886127c5-dbb1-455f-8755-402507335062.png)

## 6. Updates

## 7. TODO

## 8. Related works





