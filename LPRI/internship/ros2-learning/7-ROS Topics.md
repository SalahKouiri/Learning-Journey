<img width="1038" height="356" alt="image" src="https://github.com/user-attachments/assets/797eb8f8-c446-46c9-937f-9506c82b98b0" />

### You can list your topics 
`ros2 topic list`

### You can list your topics with more details 

`ros2 topic list -t`

### You can get info about the topic 

`ros2 topic info /turtle1/cmd_vel`

### You can see like the type of data in a topic like the variables that change 

`ros2 interface show geometry_msgs/msg
/Twist`

### You can then modify the variables with a loop : 

`ros2 topic pub /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 0.0, y: 0.0, z: 21.0}, angular: {
x: 36.0, y: 36.0, z: 21.0}}"`

### Dont forget to run your node first : 

`ros2 run turtlesim turtlesim_node`

### and then run the node controller : 
`ros2 run turtlesim turtle_teleop_key`

### you can then run this to see the graph about nodes/topics sub/pub : 
`rqt_graph`

### you can run this to see the details changing by time

`ros2 topic echo /turtle1/cmd_vel`

### you can run this to see the frequency of a topic 

`ros2 topic hz /turtle1/pose`
