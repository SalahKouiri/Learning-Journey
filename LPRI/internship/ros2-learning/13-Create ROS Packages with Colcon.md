### so to create your own package , first go into src 
`cd src`

### then run this to create the new package : 
`ros2 pkg create --build-type ament_cmake --node-name my_node my_package`

###go back to ws like this : 
`cd ~/ros2_ws/`

### then you run your package the usual colcon way : 
```
colcon build --packages-select my_package
source install/local_setup.bash
ros2 run my_package my_node
```

#### this outputed hello world my_package package 
