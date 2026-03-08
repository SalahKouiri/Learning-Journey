# Running Executables 

```
sudo apt update
sudo apt install ros-humble-turtlesim
```

### See list of packages : 
`ros2 pkg list`

### see list of packages executables  : 
`ros2 pkg executables`

### see list of executables from turtlesim package 
`ros2 pkg executables turtlesim`

### Where turrlesim ?

```
cd /opt/ros/humble/
then run : 
code .
then search : 
Ctrl+P , turtlesim
```
#### Should see turtlesim is located in : 
`share/ament_index/resource_index/packages`

### Run turtlesim. 
`ros2 run -h` 
```
ros2 run <package_name> <executable_name>
ros2 run turtlesim turtlesim_node
```
---
<img width="1389" height="525" alt="image" src="https://github.com/user-attachments/assets/7d9ac330-b2eb-415f-b3fc-cf80d6396d6b" />


### then to move the turtle run in another terminal 
`ros2 run turtlesim turtle_teleop_key`
---
<img width="1880" height="829" alt="image" src="https://github.com/user-attachments/assets/9c02a51f-b7ef-423f-a6f2-ec1a13ed3d5e" />
