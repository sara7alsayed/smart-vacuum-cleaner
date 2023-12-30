// Download and Install Ubuntu on PC
// Install ROS1
//Install Dependent ROS Packages
$ sudo apt-get install ros-noetic-joy ros-noetic-teleop-twist-joy \
  ros-noetic-teleop-twist-keyboard ros-noetic-laser-proc \
  ros-noetic-rgbd-launch ros-noetic-rosserial-arduino \
  ros-noetic-rosserial-python ros-noetic-rosserial-client \
  ros-noetic-rosserial-msgs ros-noetic-amcl ros-noetic-map-server \
  ros-noetic-move-base ros-noetic-urdf ros-noetic-xacro \
  ros-noetic-compressed-image-transport ros-noetic-rqt* ros-noetic-rviz \
  ros-noetic-gmapping ros-noetic-navigation ros-noetic-interactive-markers

//Install TurtleBot3 Packages
$ sudo apt install ros-noetic-dynamixel-sdk
$ sudo apt install ros-noetic-turtlebot3-msgs
$ sudo apt install ros-noetic-turtlebot3

//Install Simulation Package
$ cd ~/catkin_ws/src/
$ git clone -b noetic-devel https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git
$ cd ~/catkin_ws && catkin_make

//Launch simulation world (house) with turtlebot3 (waffle_pi)
$ export TURTLEBOT3_MODEL=waffle_pi
$ roslaunch turtlebot3_gazebo turtlebot3_house.launch

//Run SLAM Node
$ export TURTLEBOT3_MODEL=waffle_pi
$ roslaunch turtlebot3_slam turtlebot3_slam.launch slam_methods:=gmapping

//Run Teleportation Node to SLAM all the map
$ export TURTLEBOT3_MODEL=waffle_pi
$ roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch
// keep moving in the map using (w,a,s,d,x) till you SLAM all map

//save map
$ rosrun map_server map_saver -f ~/map


//close all terminal windows
//start simulating your turtlebot

//Launch simulation world (house) with turtlebot3 (waffle_pi)
$ export TURTLEBOT3_MODEL=waffle_pi
$ roslaunch turtlebot3_gazebo turtlebot3_house.launch

//Run Navigation Node
$ export TURTLEBOT3_MODEL=waffle_pi
$ roslaunch turtlebot3_navigation turtlebot3_navigation.launch map_file:=$HOME/map.yaml

//Estimate Initial Pose using the 2D Pose Estimate

//Set navigation goal using the 2D nav Goal