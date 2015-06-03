---
layout: post
title: Gazebo gazegoing
---

Gazebo gazegoing
-----

###Few steps to get Gazebo going on Acer Chromebook 13 running trusty (14.04)

0. Install ros indigo for armhf
1. Install gazebo 4.0 from source [gazebo source installation instruction](http://gazebosim.org/tutorials?tut=install_from_source&cat=install)
2. Run these commands to compile gazebo glue to ros

		mkdir ~/work
		cd ~/work
		
		mkdir -p ~/ros/src
		cd ~/ros/src
		catkin_init_workspace
		
		git clone https://github.com/ros-simulation/gazebo_ros_pkgs.git -b indigo-devel
		git clone https://github.com/ros-simulation/gazebo_ros_demos.git
		cd ..
		catkin_make

3. Install couple of missing packages

		sudo apt-get install ros-indigo-joint-state-controller
		sudo apt-get install ros-indigo-effort-controllers

4. Launch gazebo simulation

		roslaunch rrbot_gazebo rrbot_world

