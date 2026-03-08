<img width="1073" height="461" alt="image" src="https://github.com/user-attachments/assets/74b978a9-1e44-4ed6-b2dd-86df9d9779f8" />

### first thing you list the services with types 

`ros2 service list -t`

### then you can find services with similar service types 

`ros2 service find rcl_interfaces/srv/GetParameterTypes`

### you can interface a service type 
`ros2 interface show rcl_interfaces/srv/GetParameterTypes`

### you can call a service for example 

`ros2 service call /clear std_srvs/srv/Empty `
