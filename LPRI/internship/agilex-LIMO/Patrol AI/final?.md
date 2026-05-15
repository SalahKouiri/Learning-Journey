`ros2 launch limo_bringup limo_start.launch.py`

`ros2 launch rosbridge_server rosbridge_websocket_launch.xml`

`ros2 launch orbbec_camera dabai.launch.py enable_color:=true enable_depth:=true enable_point_cloud:=true enable_ir:=false point_cloud_qos:=SENSOR_DATA depth_qos:=SENSOR_DATA`

`ros2 run tf2_ros static_transform_publisher 0.084 0.0 0.01 0.0 0.0 0.0 base_link camera_link`

`ros2 run image_transport republish raw compressed --ros-args --remap in:=/camera/color/image_raw --remap out/compressed:=/camera/color/image_raw/compressed`

`python3 -m http.server 7000`

`x11vnc -display :0 -forever -nopw -shared -rfbport 5900`

`websockify --web /usr/share/novnc/ 6080 localhost:5900`

`python3 dashboard_commander.py`

`ros2 run limo_bringup spotter.py`
