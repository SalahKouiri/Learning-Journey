<img width="1068" height="395" alt="image" src="https://github.com/user-attachments/assets/5a51204d-d333-431a-8b5b-f86e8b4deb86" />

### run the node info then look at the aciton clients and then run the action info
`ros 2 action info /turtle1/rotate_absolute`

### you can even list the actions directly with 

`ros2 action list -t`

### then you can interface to know about the structure 

`ros2 interface show turtlesim/action/
RotateAbsolute`

### and you can send goals using the action variabls that show in the interface just like we did with topics and x y z earlier 

`ros2 action  send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.5}"`
