# eDO moveit package

This package plans trajectories of eDO robots.
It works either with a fake (simulated) robot or with an actual eDO.

![Edo moveit screen](img/screen.png)

## How to install this package
```
cd ros_ws/src
git clone https://github.com/ymollard/eDO_moveit.git
git clone https://github.com/ymollard/eDO_control.git
git clone https://github.com/Comau/eDO_core_msgs.git
git clone https://github.com/Comau/eDO_description.git
pip install getkey

```

## Quickstart
Start MoveIt with a fake eDO:
```
roslaunch edo_moveit demo.launch simulated:=true
```
In order to start MoveIt with an actual eDO, connect the RJ45 cable to eDO, switch your network card to manual addressing with an IP (e.g. 10.42.0.1) and run:
```
# Don't forget to set up your environment variables
export ROS_MASTER_URI=http://10.42.0.49:11311
export ROS_IP=10.42.0.1    # Your local workstation on which you're running MoveIt

# Calibration procedure is compulsory after any robot boot  
roslaunch edo_control calibrate.launch
 
# Then you can start MoveIt
roslaunch edo_moveit demo.launch
```
