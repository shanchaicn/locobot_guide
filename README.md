## 00.Test
```
ls /dev | grep ttyDXL
ls /dev | grep rplidar
ping -c 2 192.168.186.2
```

## 0. ssh
```
ssh -X username@hostanme.local
```

We need to change the 'hostname.local' into the IP address of NUC. 
```
ssh -x locobot2@192.168.8.108
```
To open a new window for the ternimal(might not work sometimes):
```
/usr/bin/dbus-launch /usr/bin/gnome-terminal &
```
## 1. nav - new map
[The tutorial For Nav stack](https://docs.trossenrobotics.com/interbotix_xslocobots_docs/ros1_packages/navigation_stack_configuration.html)
```
roslaunch interbotix_xslocobot_nav xslocobot_nav.launch robot_model:=locobot_base use_lidar:=true rtabmap_args:=-d
```
- `rtabmp_args:=-d` means create a new map. If you want to continue doing mapping, you need to change to this`rtabmap_args:='--Rtabmap/StartNewMapOnLoopClosure true'`

- If you want to save many maps in your locobot, you need to add `database_path:="~/.ros/rtabmap.db"` to the end of command line. For example, the name you want is DLAR_LAB, you can use 
```
roslaunch interbotix_xslocobot_nav xslocobot_nav.launch robot_model:=locobot_base use_lidar:=true rtabmap_args:=-d database_path:="~/.ros/rtabmap_DLAR_LAB.db" 
```
> :warning: You need to make sure that the `database_path` you define are the same in mapping and localization.
## 2. rviz (On remote computer)
```
roslaunch interbotix_xslocobot_descriptions remote_view.launch rviz_frame:=map
```
>  If the Rviz get stuck, you can try to uncheck some useless selections(camera,rtab-map).
## 3. mapping tools
### Controller
```
roslaunch interbotix_xslocobot_joy xslocobot_joy.launch robot_model:=locobot_base launch_driver:=false
```
### rqt tool
```
rosrun rqt_robot_steering rqt_robot_steering 
```
And change the topic to `/mobile_base/cmd_vel`

### teleop_twist_keyboard
```
rosrun teleop_twist_keyboard teleop_twist_keyboard.py cmd_vel:=/mobile_base/cmd_vel _speed:=0.3 _turn:=0.3
```
## 4. Localization
```
roslaunch interbotix_xslocobot_nav xslocobot_nav.launch robot_model:=locobot_base use_lidar:=true localization:=true
```
> :warning: You also need to change the `database_path`:
```
roslaunch interbotix_xslocobot_nav xslocobot_nav.launch robot_model:=locobot_base use_lidar:=true localization:=true database_path:="~/.ros/rtabmap_DLAR_LAB.db"
```

Open rviz again:
```
roslaunch interbotix_xslocobot_descriptions remote_view.launch rviz_frame:=map
```
## 5. Visualization (Publish markers) 
> :warning: You need to make sure that the `map_name` you define are the same during the following the step from 5 to 7.
> :warning: need run localization launch file (step4) firstly
```
roslaunch vehicle_inspection rviz_marker.launch map_name:=DLAR_LAB
```
Add a marker in rviz if you haven't done it yet.
## 6. Save points manually
> :warning: need run localization launch file (step4) firstly
> You cannot create a file with an exist name unless you delete the old `.yaml` file. It will not overwritten the previous points file.
```
roslaunch vehicle_inspection save_points.launch map_name:=DLAR_LAB
```
Press space ` ` or `s` to save a new point.
Use `ctrl+c` to end.
> The data will be saved at `/home/locobot2/interbotix_ws/src/vehicle_inspection/config/points_{map_name}.yaml`.
## 7. Waypoints move base on the points we saved
> :warning: need run localization launch file (step4) firstly
The default value of time_stop for data collecting is 0s.
```
roslaunch vehicle_inspection waypoints_move_py.launch map_name:=DLAR_LAB time_stop:=240
```
> The data will be saved at `/home/locobot2/interbotix_ws/src/vehicle_inspection/config/data_{map_name}.yaml`
You can run this launch file many times, and the time data will all save to the same one file.
## 8. Data backup files
It will be saved into SD card. The path is `/media/{user_name}/{your_device_name}`. Rtabmap database, the points and the data will be save automatically.

You can also do it manually:
- Save the database of rtab-map:
```
rosservice call /backup_file
```
- Save the data of saved points and timestamp:
```
rosservice call /backup_folder
```

### Some changes
/home/locobot2/interbotix_ws/src/interbotix_ros_toolboxes/interbotix_common_toolbox/interbotix_tf_tools/src/tf_rebroadcaster.cpp
