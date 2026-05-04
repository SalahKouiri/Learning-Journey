###this is the process to get the mapping and navigation running . 
---
####mapping : 

first you gotta start the robot , `ros2 launch limo_bringup limo_start.launch.py` 
second terminal you run `ros2 launch limo_bringup limo_mapping.launch.py`
and to save your map run `ros2 service call /slam_toolbox/save_map slam_toolbox_msgs/srv/SaveMap "{name: {data: 'my'}}"` , dont forget to cd into the maps folder . 
and of course dont forget to source before each command . 

---
####navigation : 

start your robot , then run this for the navigation `ros2 launch limo_bringup limo_nav2.launch.py map:=/home/agilex/limo_ros2_ws/src/limo_ros2/limo_bringup/maps/patron.yaml`
then run this to start the camera `ros2 launch orbbec_camera dabai.launch.py enable_color:=true enable_depth:=true enable_point_cloud:=true enable_ir:=false point_cloud_qos:=SENSOR_DATA depth_qos:=SENSOR_DATA`
and run this to fix the camera tf `ros2 run tf2_ros static_transform_publisher 0.084 0.0 0.01 0.0 0.0 0.0 base_link camera_link`

dont forget to always source , and if you want to colcon build the limo_bringup , run this `colcon build --packages-select limo_bringup --symlink-install`


that s most of it 

