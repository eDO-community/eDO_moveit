# eDO moveit package

This package plans trajectories of eDO robots.
It works either with a fake (simulated) robot or with an actual eDO.

![Edo moveit screen](img/screen.png)

## How to start?
Start MoveIt with a fake eDO:
```
roslaunch edo_moveit demo.launch simulated:=true
```

Start MoveIt with an actual eDO:
```
 # Calibration procedure is compulsory after any robot boot  
 roslaunch edo_control calibrate.launch
 
 roslaunch edo_moveit demo.launch
```