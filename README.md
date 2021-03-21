This is my submission for the final project of the Udacity Self-Driving Car Nanodegree: Programming a Real Self-Driving Car. For more information about the project, see the project introduction [here](https://classroom.udacity.com/nanodegrees/nd013/parts/6047fe34-d93c-4f50-8336-b70ef10cb4b2/modules/e1a23b06-329a-4684-a717-ad476f0d8dff/lessons/462c933d-9f24-42d3-8bdc-a08a5fc866e4/concepts/5ab4b122-83e6-436d-850f-9f4d26627fd9).

## Setup

For this project, I used the Udacity workspace with all the necessary installations and dependencies available. I would like to get this running on my system (Ubuntu 20.04 with ROS Noetic) in the near future.

Following are couple of options for installations to use --

### Native Installation

* Be sure that your workstation is running Ubuntu 16.04 Xenial Xerus or Ubuntu 14.04 Trusty Tahir. [Ubuntu downloads can be found here](https://www.ubuntu.com/download/desktop).
* If using a Virtual Machine to install Ubuntu, use the following configuration as minimum:
  * 2 CPU
  * 2 GB system memory
  * 25 GB of free hard drive space

  The Udacity provided virtual machine has ROS and Dataspeed DBW already installed, so you can skip the next two steps if you are using this.

* Follow these instructions to install ROS
  * [ROS Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu) if you have Ubuntu 16.04.
  * [ROS Indigo](http://wiki.ros.org/indigo/Installation/Ubuntu) if you have Ubuntu 14.04.
* [Dataspeed DBW](https://bitbucket.org/DataspeedInc/dbw_mkz_ros)
  * Use this option to install the SDK on a workstation that already has ROS installed: [One Line SDK Install (binary)](https://bitbucket.org/DataspeedInc/dbw_mkz_ros/src/81e63fcc335d7b64139d7482017d6a97b405e250/ROS_SETUP.md?fileviewer=file-view-default)
* Download the [Udacity Simulator](https://github.com/udacity/CarND-Capstone/releases).

### Docker Installation
[Install Docker](https://docs.docker.com/engine/installation/)

Build the docker container
```bash
docker build . -t capstone
```

Run the docker file
```bash
docker run -p 4567:4567 -v $PWD:/capstone -v /tmp/log:/root/.ros/ --rm -it capstone
```

## Port Forwarding
To set up port forwarding, please refer to the [instructions from term 2](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/16cf4a78-4fc7-49e1-8621-3450ca938b77)

## Usage

1. Clone the project repository
```bash
git clone https://github.com/udacity/CarND-Capstone.git
```

2. Install python dependencies
```bash
cd CarND-Capstone
pip install -r requirements.txt
```
3. Make and run styx
```bash
cd ros
catkin_make
source devel/setup.sh
roslaunch launch/styx.launch
```
4. Run the simulator

## Project Overview

This project uses ROS nodes to implement the core functionality of the autonomous vahicle system, including, perception, planning and control. The developed code is tested in a simulator with representative functionality. The below figure (from Udacity class material) shows the system architectire diagram with the ROS topics and nodes used in the code.

<img src="https://github.com/prasadshingne/CarND-Capstone_Workspace/blob/main/output/sys_architecture.jpg"/>

### Code Structure

All of the code that is added is within CarND-Capstone_Workspace/ros/src/ within the following ROS packages --

#### CarND-Capstone_Workspace/ros/src/tl_detector/

This package contains the traffic light detection node: tl_detector.py. This node takes in data from the /image_color, /current_pose, and /base_waypoints topics and publishes the locations to stop for red traffic lights to the /traffic_waypoint topic. The /current_pose topic provides the vehicle's current position, and /base_waypoints provides a complete list of waypoints the car will be following. I have skipped the traffic light classification as it is optional and pull the traffic light state directly from the simulator.

#### CarND-Capstone_Workspace/ros/src/waypoint_updater/

This package contains the waypoint updater node: waypoint_updater.py. This node is to updates the target velocity property of each waypoint based on the traffic light state. This node subscribes to the /base_waypoints, /current_pose, /obstacle_waypoint, and /traffic_waypoint topics, and publishes a list of waypoints ahead of the car with target velocities to the /final_waypoints topic.

#### CarND-Capstone_Workspace/ros/src/twist_controller/

This package contains the files that are responsible for control of the vehicle: the node dbw_node.py and the file twist_controller.py, along with a pid and lowpass filter that you can use in your implementation. The dbw_node subscribes to the /current_velocity topic along with the /twist_cmd topic to receive target linear and angular velocities. Additionally, this node will subscribe to /vehicle/dbw_enabled, which indicates if the car is under dbw or driver control. This node will publish throttle, brake, and steering commands to the /vehicle/throttle_cmd, /vehicle/brake_cmd, and /vehicle/steering_cmd topics.

## [Rubric](https://review.udacity.com/#!/rubrics/3058/view)

### Running the Code

The code is built successfully and connects to the simulator. I had quite a challenging time to try and set up the environment natively on Ubuntu 20.04 + ROS Noetic or even the Virtual Machine for ROS + Simulator native. Hence I decided to go ahead with the Workspace available from Udacity. As such the code built successfully (see below).

<img src="https://github.com/prasadshingne/CarND-Capstone_Workspace/blob/main/output/catkin_make.jpg"/>

ROS launched successfully as below -- 

<img src="https://github.com/prasadshingne/CarND-Capstone_Workspace/blob/main/output/ros_launch.jpg"/>

and ROS connected to the simulator as shown by the short simulator output below --

<img src="https://github.com/prasadshingne/CarND-Capstone_Workspace/blob/main/output/capstone.gif"/>

### Control and Planning

Waypoints are published to plan Carla's route around the track. This code is present inside the [waypoint_updater.py](https://github.com/prasadshingne/CarND-Capstone_Workspace/blob/main/ros/src/waypoint_updater/waypoint_updater.py#L23). The waypoints are published to [/final_waypoints](https://github.com/prasadshingne/CarND-Capstone_Workspace/blob/654164699852bc41d545ae7476ef599c98d0eec0/ros/src/waypoint_updater/waypoint_updater.py#L79) and the car as able to drive through the simulator relatively smoothly. The acceleration is within +/- 10 m/s^2 and jerk is within +/- 10 m/s^3. The vehicle velocity is set based on the [waypoint target velocity](https://github.com/prasadshingne/CarND-Capstone_Workspace/blob/654164699852bc41d545ae7476ef599c98d0eec0/ros/src/waypoint_updater/waypoint_updater.py#L139).

Controller commands are published to operate Carla’s throttle, brake, and steering. This code is present in the [dbw_node.py](https://github.com/prasadshingne/CarND-Capstone_Workspace/blob/main/ros/src/twist_controller/dbw_node.py) and [twist_controller.py](https://github.com/prasadshingne/CarND-Capstone_Workspace/blob/main/ros/src/twist_controller/twist_controller.py) 

### Successful Navigation

The vehicle is able to successfuly navigate the track more than once. The simulation video is shared via YouTube.

<a href="https://www.youtube.com/watch?v=YpqVVqg7zlg" target="_blank"><img src="http://img.youtube.com/vi/YpqVVqg7zlg/0.jpg" alt="IMAGE ALT TEXT HERE" width="480" height="360" border="10" /></a>

## Notes

1. I have skipped the classification problem for the traffic lights and directly use the traffic light state within [tl_detector.py](https://github.com/prasadshingne/CarND-Capstone_Workspace/blob/654164699852bc41d545ae7476ef599c98d0eec0/ros/src/tl_detector/tl_detector.py#L105). I plan to update this project to use a pretrained classifier such as YOLO for traffic light detection in a manner similar to [this](https://github.com/yogeshgajjar/bosch-traffic-sign-detection-YOLOv3).
2. It is important to run the simulation in the Fast/Fastest setting. For a couple of runs when I had the simulator quality better, I noticed that the simulation was pretty laggy and that caused the vehicle to diverge from the waypoints. 


