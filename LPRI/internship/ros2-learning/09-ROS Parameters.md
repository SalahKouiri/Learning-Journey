<img width="880" height="505" alt="image" src="https://github.com/user-attachments/assets/f37c8bd4-dc67-4f2c-9cc6-7b7a97020ec0" />

### you can list the parameters 

`ros2 param list`

### you can get a param value 

`ros2 param get /turtlesim background_g`


### you can set a param value 

`ros2 param set /turtlesim background_g 200`

### you can see all the param values 

`ros2 param dump /turtlesim`

### you can even save them in a file 
`ros2 param dump /turtlesim > turtlesim.yaml`

### you can even load the parameters from a file 

`ros2 param load /turtlesim turtlesim.yaml`

### you can even set it at start up to those valus from a file 

`ros2 run turtlesim turtlesim_node --ros-args --params-file turtlesim.yaml`
