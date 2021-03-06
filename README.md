# Evaluation of LeGO-LOAM: Lightweight andGround-Optimized Lidar Odometry and Mapping
This is Team 11's final project git repository for NAVARCH/EECS 568, ROB 530: Mobile Robotics. The title of our project is **Evaluation of LeGO-LOAM: Lightweight and Ground-Optimized Lidar Odometry and Mapping**. Team members include: Bowen Mu, Chien Erh Lin, Haochen Wu, and Weilin Xu.

## Abstract
LeGO-LOAM, a lightweight and ground-optimized lidar odometry and mapping method, is able to do six degree-of-freedom pose estimation for real time with ground vehicles. This project will evaluate the LeGO-LOAM which reduced computational expense while keeping similar accuracy compared to LOAM method. The whole LeGO-LOAM system is implemented in Robot Operating System (ROS). The data used for simulation is raw data (with Velodyne LiDAR data and IMU) from [KITTI odometry benchmark dataset](http://www.cvlibs.net/datasets/kitti/), [utbm dataset](https://epan-utbm.github.io/utbm_robocar_dataset/) and [KAIST Urban Dataset](http://irap.kaist.ac.kr/dataset/). We compared the mapping and odometry results of these data, and analysis the relative motion and mapping error in this report. 

## Evaluation Result & Conclusion
The evaluation result on KITTI data set illustrates that position estimation error for odometry is acceptable (around 10 m) and that mapping result can provide concise but adequate information of the environment. By comparing with LOAM, LeGO-LOAM provides better accuracy on mapping and odometry tracking (both rotational and translational), and more efficient since filtering the unreliable point cloud. It is also observed that the loop closure reduces the error in odometry for LeGO-LOAM, and LOAM does not have loop closure ability. However, when the drift is large within a short time (such as sharp turn), the performance of LeGO-LOAM is negatively affected. In addition, from the test on UTBM, LeGO-LOAM also has a limited mapping and tracking ability for the roundabout trajectory. 

## Getting Started
We recommend you read through the original [LOAM paper](http://www.roboticsproceedings.org/rss10/p07.pdf) by Ji Zhang and Sanjiv Singh and the original [LeGO-LOAM paper](http://personal.stevens.edu/~benglot/Shan_Englot_IROS_2018_Preprint.pdf) by Tixiao Shan and Brendan Englot. These will give you theoretical understanding of the LOAM and LeGO-LOAM algorithm.

## LeGO-LOAM
The [LeGO-LOAM repository](https://github.com/chienerh/LeGO-LOAM) is forked from [RobustFieldAutonomyLab/LeGO-LOAM](https://github.com/RobustFieldAutonomyLab/LeGO-LOAM). We changed it to be able to run kitti dataset.

### Prerequisites
- First, install [ROS](http://wiki.ros.org/ROS/Installation). We used ROS-kinetic on Ubuntu 16.04. Please follow the instruction of ROS installation page.
- [gtsam](https://github.com/borglab/gtsam). GTSAM is a library of C++ classes that implement smoothing and mapping (SAM). You can install it by following code.
```
git clone https://bitbucket.org/gtborg/gtsam.git
cd gtsam/
mkdir build
cd build
cmake ..
make check 
make install
```
in /usr/local/lib/cmake/GTSAM/GTSAMConfig.cmake, change :17 `find_dependency` to `find_package`

### Install, Compile, and RUN
To install and compile the code, please clone this respository in src/ folder under catkin workspace.
```
cd ~/catkin_ws/src/
git https://github.com/chienerh/LeGO-LOAM.git
cd ..
catkin_make -j1
```
For the first time compiling, add -j1 after catkin_make.
```
source ./devel/setup.bash
roslaunch lego_loam run.launch  > PATH_TO_TXT_FILE.txt
rosbag play PATH_TO_BAG_FILE.bag --clock --topic /kitti/velo/pointcloud /kitti/oxts/imu
```

### LeGO-LOAM with KITTI Data
- Kitti ran with DHL-64E, so change parameters in utility.h
```
extern const int N_SCAN = 64;
extern const int Horizon_SCAN = 1800;
extern const float ang_res_x = 0.2;
extern const float ang_res_y = 0.427;
extern const float ang_bottom = 24.9;
extern const int groundScanInd = 50;
```
- In `featureAssociation.cpp` line 849 TransformToStart(), `float s = 1;`
- In `featureAssociation.cpp` line 1752 and 1758, disable TransformToEnd()
- Sample ros bag files from [this link](https://drive.google.com/open?id=1wROSNkyXATcOJRplCn-BQF1gF7OhDCha) and put it in your own `PATH_TO_BAG_FILE` folder. 
- Currently for our kitti rosbag, lidar topic name is `/kitti/velo/pointcloud`, and the imu topic name is `/kitti/oxts/imu`. Change topic names from these two to the topic name in your the rosbag if these are not the correct topic names in `run.launch` file line 16 and the rosbag play command.

### LeGO-LOAM with utbm dataset
- rosbag can be downloaded on their [website](https://epan-utbm.github.io/utbm_robocar_dataset/)
- install [GPS driver](https://github.com/epan-utbm/utbm_robocar_dataset/tree/baselines/magellan_proflex500)

## LOAM
The [loam_velodyne repository](https://github.com/chienerh/loam_velodyne) is forked from [laboshinl's version of LOAM](https://github.com/laboshinl/loam_velodyne). We changed it to be able to run kitti dataset.

### Install, Compile, and RUN
```
cd ~/catkin_ws/src/
git clone https://github.com/chienerh/loam_velodyne
cd ~/catkin_ws
catkin_make -DCMAKE_BUILD_TYPE=Release 
source ~/catkin_ws/devel/setup.bash
```
```
roslaunch loam_velodyne loam_velodyne.launch
rosbag play PATH_TO_BAG_FILE.bag 
```
### LOAM with KITTI data
- Rostopic of kitti point cloud is "/kitti/velo/pointcloud", so remap "/multi_scan_points" to "/kitti/velo/pointcloud" in `loam_velodyne.launch`.
- Set lidar to be "VLP-16" in `loam_velodyne.launch`.

### LOAM with KAIST Dataset
- Use [File Player](https://github.com/irapkaist/file_player) to publish KAIST data into ros message.
- Rostopic of KAIST dataset point cloud topic is `"/ns1/velodyne_points"`, so remap `"/multi_scan_points"` to `"/ns1/velodyne_points"` in `loam_velodyne.launch`.

## Result
### LeGO-LOAM
![seq00](/result/00_cmp.png)
![seq00](/result/00.png)
![seq08](/result/08_cmp.png)
![seq08](/result/08.png)
![seq05](/result/05_cmp.png)
![seq05](/result/05.png)
### LOAM
![loam_seq05](loam_05.png)
![loam_seq05](loam_seq_05.png)

## APPENDIX
### How to download and transform kitti to rosbag
- Download raw sequences **synced+rectified data** and **calibration** data from [The KITTI Vision Benchmark Suite](http://www.cvlibs.net/datasets/kitti/raw_data.php).
- [kitti2bag](https://github.com/tomas789/kitti2bag) is used to transform kitti raw data to rosbag.

The sequences that provide ground truth are shown in the below table. The [odometry eval kit](http://kitti.is.tue.mpg.de/kitti/devkit_odometry.zip) includes description.

The following table lists the name, start and end frame of each sequence that
has been used to extract the visual odometry / SLAM training set

| Seq |Sequence name |Start | End | Category | Size |
| --- | --- | --- | --- | --- | --- |
| 00 | 2011_10_03_drive_0027 | 000000 | 004540 | Residential | 17.6 GB |
| 01 | 2011_10_03_drive_0042 | 000000 | 001100 | Road | 4.5 GB |
| 02 | 2011_10_03_drive_0034 | 000000 | 004660 | Residential | 18.0 GB |
| 03 | 2011_09_26_drive_0067 | 000000 | 000800 | 
| 04 | 2011_09_30_drive_0016 | 000000 | 000270 | Road | 1.1 GB |
| 05 | 2011_09_30_drive_0018 | 000000 | 002760 | Residential | 10.7 GB |
| 06 | 2011_09_30_drive_0020 | 000000 | 001100 | Residential | 4.3 GB |
| 07 | 2011_09_30_drive_0027 | 000000 | 001100 | Residential | 4.3 GB |
| 08 | 2011_09_30_drive_0028 | 001100 | 005170 | Residential | 20.0 GB |
| 09 | 2011_09_30_drive_0033 | 000000 | 001590 | Residential | 6.2 GB |
| 10 | 2011_09_30_drive_0034 | 000000 | 001200 | Residential | 4.8 GB |

### How to save the odometry result
- in launch file, add 
```
  <!-- rosbag arg -->
  <arg name="ros_bag_name" default="simulation_res"/>
```
```
  <node pkg="rosbag" type="record" name="record" args="record -O /home/USER_NAME/catkin_ws/src/loam_velodyne/result/bag/$(arg ros_bag_name).bag
                                                	/integrated_to_init
							/groundtruth_pose/pose"/>/
```
- to save odometry result, in src/lib/TransformMaintence.cpp line 79, add
```
   printf("%f, %f, %f\n", transformMapped()[3], transformMapped()[4], transformMapped()[5]);
```
Then run launch file with this command outputing file to a csv
```
roslaunch loam_velodyne loam_velodyne.launch > PATH_TO_FILE.csv
```
The odmetry result in saved `PATH_TO_TXT_FILE.txt` and also in `./src/LeGO-LOAM/result/bag/`. This .bag file can transformed to .csv file by:
```
python ./src/LeGO-LOAM/result/process_rosbag.py
```
Afterwards, you can use `./src/result_plot.m` in the repo to plot and compare `PATH_TO_TXT_FILE.txt` and the ground truth odemetry txt file. You need to change variables `res_file` and `groundTruthFile` in this MATLAB to the correct name of `PATH_TO_TXT_FILE.txt` and ground truth odemetry txt.
