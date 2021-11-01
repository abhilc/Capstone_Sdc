This is the project repo for the final project of the Udacity Self-Driving Car Nanodegree: Programming a Real Self-Driving Car. For more information about the project, see the project introduction [here](https://classroom.udacity.com/nanodegrees/nd013/parts/6047fe34-d93c-4f50-8336-b70ef10cb4b2/modules/e1a23b06-329a-4684-a717-ad476f0d8dff/lessons/462c933d-9f24-42d3-8bdc-a08a5fc866e4/concepts/5ab4b122-83e6-436d-850f-9f4d26627fd9).

## Udacity's Project walkthrough was used as inspiration for the final implementation of the project

## System Architecture of Carla

<p>
    <img src="imgs/Screenshot 2021-11-01 at 5.25.00 PM.png"/>
</p>

1. As indicated in the above figure, the project uses a publisher-subscriber architecture taking advantage of Robot Operating System for message passing to drive the car fully autonomously. 
2. Similar code organisation can be found in ros/src

## Waypoint updater Node
1. This node subscribes to topics /current_pose, that provides the position, linear and angular velocities of the vehicle of message type sytx/geometry; /base_waypoints of message type Lane. This provides all the points ahead and behind the vehicle; and /traffic_waypoint which provides information about the traffic lights
2. In waypoint updater node, the required waypoints are published over the topic /final_waypoints. Before these are published, preprocessing is done in waypoint_updater.py to
   a. Only get the waypoints ahead of the car and not behind
   b. Assign velocities to each waypoint depending on the color of the traffic sign
3. The information is gained via defined callback functions(with suffix cb). This node publishes final waypoints at 50 Hz. This part of the code can be found in the loop function

## Traffic light detection Node
1. This node subscribes to necessary topics as defined from lines 30-41 in tl_detector.py 
2. The goal of this node is to find the closest node in the waypoints list from the traffic light ahead of the car, and set the velocity of this point to zero, such that the vehicle may not cross the red light
3. The logic to find the above is implemented in the function process_traffic_lights which is called through the image_callback function
4. The information of the closest waypoint depending on the state of the traffic light is finally consumed in waypoint_updater.py file. If such point is found, the waypoints leading upto this point are set to deceleration velocities so that the car decelerates smoothly before coming to a stand still state

## Drive by wire Node
1. This node consumes the steering, brake and throttle command from the twist controller node and passes the information to the actuators. 

## Twist Controller Node
The goal of this node is to translate the computed linear velocity, angular velocity and current velocity into steering command, brake command and throttle. 
1. This is in-turn achieved by the pid controller defined in the PID class in file pid.py
2. The drive-by-wire(dbw node) subscribes to twist controller to consume this information to provide steering commands to the vehicle
3. It is also taken care in this file, to only return these commands if the car is autonomously driving. This is done by checking the flag dbw_enabled

## Observation
The car was able to successfully autonomously drive in the simulation without unnecessary turns or lane changes, not exceeding the max jerk limits whilst braking at red traffic lights. 

Please use **one** of the two installation options, either native **or** docker installation.

## Native Installation

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
  * Use this option to install the SDK on a workstation that already has ROS installed: [One Line SDK Install (binary)](https://bitbucket.org/DataspeedInc/dbw_mkz_ros/src/master/ROS_SETUP.md)
* Download the [Udacity Simulator](https://github.com/udacity/CarND-Capstone/releases).

## Docker Installation
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

## Real world testing
1. Download [training bag](https://s3-us-west-1.amazonaws.com/udacity-selfdrivingcar/traffic_light_bag_file.zip) that was recorded on the Udacity self-driving car.
2. Unzip the file
```bash
unzip traffic_light_bag_file.zip
```
3. Play the bag file
```bash
rosbag play -l traffic_light_bag_file/traffic_light_training.bag
```
4. Launch your project in site mode
```bash
cd CarND-Capstone/ros
roslaunch launch/site.launch
```
5. Confirm that traffic light detection works on real life images
