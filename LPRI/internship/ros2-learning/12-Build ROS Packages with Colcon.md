<img width="966" height="638" alt="image" src="https://github.com/user-attachments/assets/315736e1-3d61-42f0-84fd-6dc242396dc8" />

---

<img width="459" height="750" alt="image" src="https://github.com/user-attachments/assets/12839377-0fc3-4ff0-bd53-97440d1aa339" />





### the first thing i did was installing colcon: 
`sudo apt install python3-colcon-common-extensions`


### then you can create your workspace like this : 

`mkdir -p ~/ros2_ws/src`

### and direct to it like this : 

`cd ~/ros2_ws`

### then ran this command to do something idk 
`echo "source /usr/share/colcon_argcomplete/hook/colcon-argcomplete.bash" >> ~/.bashrc`

### and then ran this to install what i got from the github repo into our workspace 
`olcon build --symlink-install`


### you can use `ls` to see the folders in your workspace 


### run this to source stuff 
`source install/local_setup.bash`

### and now you can run the turtlesim from the github tuto 
`ros2 run turtlesim turtlesim_node`

#### I modified a value inside the source code just to make sure that it is changing , so i changed the name of the windowtitle to salah inside turtle_frame.cpp and then did the 
```
colcon buil --symlink-install
source install/local_setup.bash
ros2 run turtlesim turtlesim_node
```
### and this was the result : 
<img width="509" height="531" alt="image" src="https://github.com/user-attachments/assets/b8d79a46-d2a2-4427-a434-15e4288bfa7a" />
