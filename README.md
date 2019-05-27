# eDO moveit package

This package plans trajectories of eDO robots and their electric gripper.
It works either with a fake (simulated) robot or with an actual eDO.

![Edo moveit screen](img/screen.png)

## How to install this package
```
cd ros_ws/src
git clone https://github.com/ymollard/eDO_moveit.git
git clone https://github.com/ymollard/eDO_control.git
git clone https://github.com/ymollard/eDO_description.git
git clone https://github.com/ymollard/eDO_core_msgs.git
pip install getkey numpy
cd ..
catkin_make
```

## Quickstart with a fake (simulated) eDO robot
Start MoveIt with a fake eDO:
```
roslaunch edo_moveit demo.launch simulated:=true
```
You can then use the regular MoveIt GUI or generate your own trajectories:

1. In the `Motion Planning` widget located at the bottom left of the RVIz window, select tab `Planning`
2. In the 3D robot view, move the blue ball corresponding to the end effector somewhere in space
3. The orange robot configuration is your new position target, move or rotate it as you want
4. Click `Plan and Execute` to start moving your simulated robot at this position

## Quickstart with a real eDO robot
Although other configurations are possible, this package intends to run on a deported workstation connected through Ethernet to a 6-axis e.Do on which an electric gripper is mounted.
Other configurations (Wifi, no gripper, run on the internal Pi...) might need to tweak a bunch of parameters, as they have not been tested. We do not advise Wifi connection: since joint commands are sent over the network, it might result is jerky trajectories because of Wifi lags.

### 1. Network configuration to be done once
#### 1.1 Local workstation network
Before anything, configure your workstation's IP address so that's in the 10.42.0.XXX network. Here we assume `10.42.0.1` so **make sure** you replace all `10.42.0.1` by you own IP if you choose another one.

#### 1.2 EDo's raspi configuration over Ethernet
Allow your robot to communicate with your local workstation through Ethernet with this configuration step.
Connect to the EDo's internal Raspberry Pi via SSH:
```
ssh edo@10.42.0.49
```
Default password is `raspberry`. You should see an italian-speaking `comandi tmux` prompt with explanations about rostopic.
If you don't, you have trouble in networking configuraiton, or the internal Raspberry Pi does not run.

We need to edit the `ministarter` script:
```
nano ~/ministarter
``` 
Locate these 2 ROS configuration lines:
```
export ROS_MASTER_URI=http://192.168.12.1:11311
export ROS_IP=192.168.12.1
```
and replace them by the IP address of the robot's Ethernet interface:
```
export ROS_MASTER_URI=http://10.42.0.49:11311
export ROS_IP=10.42.0.49
```
Press `ctrl=X`, `y` and then `Enter` to valide the filename and overwrite the file.

Type `sudo reboot` to reboot the robot and wait for it to be up again.

#### 1.3 Local workstation ROS config

A setup script `start.bash` configures the environment variables for you every time you want to work with your EDo.
It adds a yellow-coloured prefix to your prompt with the name of the right ROS master URI when enabled.

```
roscd edo_control
./start.bash
```

Check that everything is fine: If this command returns the message below, you're done for the ROS network configuration!
```
[http://10.42.0.49:11311] me@workstation :~$ rostopic echo /machine_state -n1
current_state: 0
opcode: 0
```
### 2. Let's start MoveIt with the actual eDO
### 2.1. Calibration procedure is compulsory after any robot boot
```
 roslaunch edo_control calibrate.launch
```
You should first see a `JOINT_UNCALIBRATED` warning message and then the explanations for the calibration procedure.
Follow the instructions every time you see `Calibrating joint X`, press left and right arrow keys to align the joint center marker and press Enter to switch to the next joint.

Go on with the entire calibration before continuing.

### 2.2 Start MoveIt
```
roslaunch edo_moveit demo.launch
```
You can then use the regular MoveIt GUI or generate your own trajectories:

1. In the `Motion Planning` widget located at the bottom left of the RVIz window, select tab `Planning`
2. In the 3D robot view, move the blue ball corresponding to the end effector somewhere in space
3. The orange robot configuration is your new position target, move or rotate it as you want
4. Click `Plan and Execute` to start moving your real robot at this position

### 2.3 Open and close the gripper
Only the 2-finger electric gripper is supported in this package.
You can publish `true` for opening the gripper (60mm range) and `false` for closing the gripper (0mm range) on the following topic:
```
rostopic pub /open_gripper std_msgs/Bool "data: true"
```

### Important warnings
Here are the big missing features and some clues if you want to implemente them:
* Control won't work without the electric gripper mounted. TODO: Update the `joint_mask` data field everywhere: `joint_mask = 127` means that 7 joints are mounted (joint 7 is the gripper).

### Troubleshooting
#### Motor brakes activate randomly when the robot is moving
If your trajectory is interrupted by sudden activation of motor brakes followed by automatic brake desengaging and operation resuming, make sure your robot has been calibrated carefully and precisely. Otherwise you might have sollicited too much your motors (too much force or too fast).


