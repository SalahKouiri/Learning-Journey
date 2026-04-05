`ros2 launch limo_description gazebo_models_diff.launch.py`

`ros2 run twist_mux twist_mux --ros-args --params-file ~/limo_ros2_ws/config/twist_mux.yaml -r /cmd_vel_out:=/cmd_vel`

`ros2 launch nav2_bringup localization_launch.py use_sim_time:=true map:=/home/salah/limo_ros2_ws/maps/my_map.yaml params_file:=/home/salah/limo_ros2_ws/config/nav2_params.yaml`

`ros2 launch nav2_bringup navigation_launch.py use_sim_time:=true params_file:=/home/salah/limo_ros2_ws/config/nav2_params.yaml cmd_vel:=/cmd_vel_nav`

`ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args --remap cmd_vel:=/cmd_vel_manual`
