the goal of this dashboard is for non technical people to be able to control the robot without running commands and all , and also it s a learning experience for me it will teach me how ros systems work and how we can bridge between ros ssytems and webpages . 

the first thing i did was fidning out how i will connect a webpage with a robot , so i started searching and found ROSBridge and roslibjs , these two will help the page communicate with the robot , so some of the main functions of rosbridge include : 
Creating a WebSocket connection, which allows bidirectional communication between the robot and web browsers⁠⁠

Exposing ROS's publish/subscribe functionality, enabling web applications to publish and subscribe to ROS topics⁠⁠

Bridging the gap between ROS's TCP/UDP socket communication and web browsers, which don't have direct access to these protocols

first you need to install the rosbridge server , you can do that with : `sudo apt install ros-humble-rosbridge-server`

then you can start the server with : `ros2 launch rosbridge_server main_rosbridge_launch.py`

in the real system i used this instead : `ros2 launch rosbridge_server rosbridge_websocket_launch.xml`

now that we know how we will connect the web dashboard and the ros system the next thing is to start building the dashboard , so created the folder webpage_ws and the file index.html , and i use roslibjs to connect to rosbridge , and i added a placeholder for the adress of the rosbridge and a button connect , so it will establish the connection between the webpage and the machine 
here is the first code : 
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ROS Web Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/roslib@1/build/roslib.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 20px;
            background-color: #f0f0f0;
        }

        .connection-form {
            width: 100%;
            margin-bottom: 20px;
        }
    </style>
</head>

<body>
    <div class="connection-form">
        <input type="text" id="rosbridge-address" placeholder="ws://localhost:9090">
        <button onclick="connectToROS()">Connect</button>
    </div>

    <script>
        let ros;

        function connectToROS() {
            const rosbridgeAddress = document.getElementById('rosbridge-address').value;
            ros = new ROSLIB.Ros({
                url: rosbridgeAddress
            });

            ros.on('connection', function () {
                console.log('Connected to rosbridge server.');
                // Connected ! 
                // We can now pull date from the topics. 
            });

            ros.on('error', function (error) {
                console.error('Error connecting to rosbridge server:', error);
            });

            ros.on('close', function () {
                console.log('Connection to rosbridge server closed.');
            });
        }

    </script>
</body>

</html>
```
and first you need to serve it to the internet :  
```
cd ~/webpage_ws
python3 -m http.server 7000
```
and to get the webpage adress i used : `webpage_address`

and it looks like this : 

<img width="1109" height="570" alt="image" src="https://github.com/user-attachments/assets/40c85fcf-4e49-4b4f-aba1-8a647f6eacdd" />


and in order to get the rosbridge address it s similar to the webpage address , you need to use : ` rosbridge_address `

so now when you enter the rosbridge address and click connect the connection gets established with the ros

now that we have a successful connection , we want to get some data from our ros topics , if you run `ros2 topic list` , you will see that we have many types of data that we can visualise .

<img width="795" height="1097" alt="image" src="https://github.com/user-attachments/assets/b744ba29-87ff-40a1-abea-dc41ff347fba" />


so the first thing i wanted to show was the position x,y of the robot , so i used /odom topic the odometry topic , so we specify the topic i called it odomtopic , passed the ros object the name of the topic and the message type , then we subscribe using .subscrive and add a callbackfunciton and after that i displayed position-x , position-y , and added a bit of styling , and the second code looked like this : 
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ROS Web Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/roslib@1/build/roslib.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 20px;
            background-color: #f0f0f0;
        }

        .dashboard {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
        }

        .connection-form {
            width: 100%;
            margin-bottom: 20px;
        }


        .position-display {
            width: 200px;
            padding: 10px;
            background-color: #fff;
            border-radius: 5px;
        }
    </style>
</head>

<body>
    <div class="connection-form">
        <input type="text" id="rosbridge-address" placeholder="ws://localhost:9090">
        <button onclick="connectToROS()">Connect</button>
    </div>
    <div class="dashboard">
        <div class="position-display">
            <h3>Position</h3>
            <p>X: <span id="position-x">0</span></p>
            <p>Y: <span id="position-y">0</span></p>
        </div>
    </div>

    <script>
        let ros;

        function connectToROS() {
            const rosbridgeAddress = document.getElementById('rosbridge-address').value;
            ros = new ROSLIB.Ros({
                url: rosbridgeAddress
            });

            ros.on('connection', function () {
                console.log('Connected to rosbridge server.');
                setupTopics();
            });

            ros.on('error', function (error) {
                console.error('Error connecting to rosbridge server:', error);
            });

            ros.on('close', function () {
                console.log('Connection to rosbridge server closed.');
            });
        }

        function setupTopics() {
            // Set up odometry topic
            const odomTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/odom',
                messageType: 'nav_msgs/Odometry'
            });

            odomTopic.subscribe(function (message) {
                document.getElementById('position-x').textContent = message.pose.pose.position.x.toFixed(2);
                document.getElementById('position-y').textContent = message.pose.pose.position.y.toFixed(2);
            });
        }


    </script>
</body>

</html>
```

and the updated page looked like this : 

<img width="1100" height="482" alt="image" src="https://github.com/user-attachments/assets/30a6ae36-57e9-4a5c-8d19-7ffff4b3b235" />

now if i moved the robot with a command the odom data updates in real time in the webpage , but it would be much nicer to control it from the webpage , so that was the next step , 
i chose the joystick way because it s the most intiutive way , and i used a small library called nippleJS (https://github.com/yoannmoinet/nipplejs)
so i included it and i set up a new topic called cmdVELTopic just like the odomtopic , and wrote its functioning code and some styling , here is the third code : 
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ROS Web Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/roslib@1/build/roslib.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/nipplejs/0.9.0/nipplejs.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 20px;
            background-color: #f0f0f0;
        }

        .dashboard {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
        }

        .connection-form {
            width: 100%;
            margin-bottom: 20px;
        }

        .joystick-container {
            width: 200px;
            height: 200px;
            background-color: #ddd;
            border-radius: 50%;
            position: relative;
            overflow: hidden;
        }

        .position-display {
            width: 200px;
            padding: 10px;
            background-color: #fff;
            border-radius: 5px;
        }
    </style>
</head>

<body>
    <div class="connection-form">
        <input type="text" id="rosbridge-address" placeholder="ws://localhost:9090">
        <button onclick="connectToROS()">Connect</button>
    </div>
    <div class="dashboard">
        <div class="joystick-container" id="joystick"></div>
        <div class="position-display">
            <h3>Position</h3>
            <p>X: <span id="position-x">0</span></p>
            <p>Y: <span id="position-y">0</span></p>
        </div>
    </div>

    <script>
        let ros;
        let cmdVelTopic;
        let joystick;

        function connectToROS() {
            const rosbridgeAddress = document.getElementById('rosbridge-address').value;
            ros = new ROSLIB.Ros({
                url: rosbridgeAddress
            });

            ros.on('connection', function () {
                console.log('Connected to rosbridge server.');
                setupTopics();
            });

            ros.on('error', function (error) {
                console.error('Error connecting to rosbridge server:', error);
            });

            ros.on('close', function () {
                console.log('Connection to rosbridge server closed.');
            });
        }

        function setupTopics() {
            // Set up cmd_vel topic
            cmdVelTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/cmd_vel',
                messageType: 'geometry_msgs/Twist'
            });

            // Set up odometry topic
            const odomTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/odom',
                messageType: 'nav_msgs/Odometry'
            });

            odomTopic.subscribe(function (message) {
                document.getElementById('position-x').textContent = message.pose.pose.position.x.toFixed(2);
                document.getElementById('position-y').textContent = message.pose.pose.position.y.toFixed(2);
            });

            // Set up joystick
            setupJoystick();
        }

        function setupJoystick() {
            const options = {
                zone: document.getElementById('joystick'),
                mode: 'static',
                position: {left: '50%', top: '50%'},
                color: 'blue',
                size: 150
            };

            joystick = nipplejs.create(options);

            joystick.on('move', function (evt, data) {
                const maxSpeed = 1.0; // m/s
                const maxTurn = 1.0; // rad/s

                const linear = Math.sin(data.angle.radian) * maxSpeed * data.distance / 75;
                const angular = -Math.cos(data.angle.radian) * maxTurn * data.distance / 75;

                const twist = new ROSLIB.Message({
                    linear: {x: linear, y: 0, z: 0},
                    angular: {x: 0, y: 0, z: angular}
                });

                cmdVelTopic.publish(twist);
            });

            joystick.on('end', function () {
                const twist = new ROSLIB.Message({
                    linear: {x: 0, y: 0, z: 0},
                    angular: {x: 0, y: 0, z: 0}
                });

                cmdVelTopic.publish(twist);
            });
        }
    </script>
</body>

</html>
```
and it looks like this : 

<img width="1102" height="579" alt="image" src="https://github.com/user-attachments/assets/c17e88d9-1a63-418d-bc2d-c03004485a6a" />


now the last step was setting up the camera display , just like the other topics we setup the imagetopic , name message type and subscribe to the topic and add a function callback to display th thing , add some styling and here is the final code : 

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ROS Web Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/roslib@1/build/roslib.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/nipplejs/0.9.0/nipplejs.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 20px;
            background-color: #f0f0f0;
        }

        .dashboard {
            display: flex;
            flex-wrap: wrap;
            gap: 20px;
        }

        .connection-form {
            width: 100%;
            margin-bottom: 20px;
        }

        .joystick-container {
            width: 200px;
            height: 200px;
            background-color: #ddd;
            border-radius: 50%;
            position: relative;
            overflow: hidden;
        }

        .image-view {
            width: 640px;
            height: 480px;
            background-color: #000;
        }

        .position-display {
            width: 200px;
            padding: 10px;
            background-color: #fff;
            border-radius: 5px;
        }
    </style>
</head>

<body>
    <div class="connection-form">
        <input type="text" id="rosbridge-address" placeholder="ws://localhost:9090">
        <button onclick="connectToROS()">Connect</button>
    </div>
    <div class="dashboard">
        <div class="joystick-container" id="joystick"></div>
        <img class="image-view" id="camera-feed" alt="Camera Feed">
        <div class="position-display">
            <h3>Position</h3>
            <p>X: <span id="position-x">0</span></p>
            <p>Y: <span id="position-y">0</span></p>
        </div>
    </div>

    <script>
        let ros;
        let cmdVelTopic;
        let joystick;

        function connectToROS() {
            const rosbridgeAddress = document.getElementById('rosbridge-address').value;
            ros = new ROSLIB.Ros({
                url: rosbridgeAddress
            });

            ros.on('connection', function () {
                console.log('Connected to rosbridge server.');
                setupTopics();
            });

            ros.on('error', function (error) {
                console.error('Error connecting to rosbridge server:', error);
            });

            ros.on('close', function () {
                console.log('Connection to rosbridge server closed.');
            });
        }

        function setupTopics() {
            // Set up cmd_vel topic
            cmdVelTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/cmd_vel',
                messageType: 'geometry_msgs/Twist'
            });

            // Set up camera topic
            const imageTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/camera/image_raw/compressed',
                messageType: 'sensor_msgs/CompressedImage'
            });

            imageTopic.subscribe(function (message) {
                document.getElementById('camera-feed').src = "data:image/jpeg;base64," + message.data;
            });

            // Set up odometry topic
            const odomTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/odom',
                messageType: 'nav_msgs/Odometry'
            });

            odomTopic.subscribe(function (message) {
                document.getElementById('position-x').textContent = message.pose.pose.position.x.toFixed(2);
                document.getElementById('position-y').textContent = message.pose.pose.position.y.toFixed(2);
            });

            // Set up joystick
            setupJoystick();
        }

        function setupJoystick() {
            const options = {
                zone: document.getElementById('joystick'),
                mode: 'static',
                position: {left: '50%', top: '50%'},
                color: 'blue',
                size: 150
            };

            joystick = nipplejs.create(options);

            joystick.on('move', function (evt, data) {
                const maxSpeed = 1.0; // m/s
                const maxTurn = 1.0; // rad/s

                const linear = Math.sin(data.angle.radian) * maxSpeed * data.distance / 75;
                const angular = -Math.cos(data.angle.radian) * maxTurn * data.distance / 75;

                const twist = new ROSLIB.Message({
                    linear: {x: linear, y: 0, z: 0},
                    angular: {x: 0, y: 0, z: angular}
                });

                cmdVelTopic.publish(twist);
            });

            joystick.on('end', function () {
                const twist = new ROSLIB.Message({
                    linear: {x: 0, y: 0, z: 0},
                    angular: {x: 0, y: 0, z: 0}
                });

                cmdVelTopic.publish(twist);
            });
        }
    </script>
</body>

</html>
```
and it looked like this :



![Video Project 1](https://github.com/user-attachments/assets/584b91eb-c73e-4152-a735-e3091c7cdc55)



