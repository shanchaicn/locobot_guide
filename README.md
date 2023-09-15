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
ssh -x locobot2@10.144.113.57
```
To open a new window for the ternimal(might not work sometimes):
```
/usr/bin/dbus-launch /usr/bin/gnome-terminal &
```
## 1. nav - new map
[The tutorial For Nav stack](https://docs.trossenrobotics.com/interbotix_xslocobots_docs/ros1_packages/navigation_stack_configuration.html)
```
roslaunch interbotix_xslocobot_nav xslocobot_nav.launch robot_model:=locobot_base use_lidar:=true rtabmap_args:=-d"
```
- `rtabmp_args:=-d` means create a new map. If you want to continue doing mapping, you need to change to this`rtabmap_args:='--Rtabmap/StartNewMapOnLoopClosure true'`

- If you want to save many maps in your locobot, you need to add `database_path:="~/.ros/rtabmap.db"` to the end of command line. For example, the name you want is DLAR_LAB, you can use 
```
roslaunch interbotix_xslocobot_nav xslocobot_nav.launch robot_model:=locobot_base use_lidar:=true rtabmap_args:=-d database_path:="~/.ros/rtabmap_DLAR_LAB.db" 
```


## 2. rviz -remote
```
roslaunch interbotix_xslocobot_descriptions remote_view.launch rviz_frame:=map
```
## 3. mapping tools
### Controller
```
roslaunch interbotix_xslocobot_joy xslocobot_joy.launch robot_model:=locobot_base launch_driver:=false
```
### rqt tool
```
rosrun rqt_robot_steering rqt_robot_steering 
```

## 4. Localization
```
roslaunch interbotix_xslocobot_nav xslocobot_nav.launch robot_model:=locobot_base use_lidar:=true localization:=true
```
You also need to change the `database_path`: 
```
roslaunch interbotix_xslocobot_nav xslocobot_nav.launch robot_model:=locobot_base use_lidar:=true localization:=true database_path:="~/.ros/rtabmap_DLAR_LAB.db
```

Open rviz again:
```
roslaunch interbotix_xslocobot_descriptions remote_view.launch rviz_frame:=map
```
## 5. Visualization
```
roslaunch vehicle_inspection rviz_marker.launch map_name:=DLAR_LAB
```
Add a marker in rviz if you haven't done it yet.
## 6. Save points manually
> What you need to pay attention to is that when you run this launch the old file with the same name will to oerwritten. 
```
roslaunch vehicle_inspection save_points.launch map_name:=DLAR_LAB
```
Press space ` ` or `s` to save a new point.
Use `ctrl+c` to end.
## 6. Waypoints move base on the points we saved
The default value of time_stop for data collecting is 0s.
```
roslaunch vehicle_inspection waypoints_move_py.launch map_name:=DLAR_LAB time_stop:=240
```
