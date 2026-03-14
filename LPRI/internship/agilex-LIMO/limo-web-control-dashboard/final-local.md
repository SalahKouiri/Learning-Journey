So here is the final local version : 
first thing you need to do is
` cd ~/limo_ros2_ws ` then : 
`source /opt/ros/foxy/setup.bash `
and then `source install/setup.bash`
and then `ros2 launch limo_bringup limo_start.launch.py`
 this will launch the limo 

then you need to launch the rosbridge like this : 
again source like this : 
```
source /opt/ros/foxy/setup.bash
source install/setup.bash

```
and then launch rosbridge : 

` ros2 launch rosbridge_server rosbridge_websocket_launch.xml `

now you need to start your code local server , 
btw here is the final code : 

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
            align-items: flex-start;
        }

        .connection-form {
            width: 100%;
            margin-bottom: 20px;
        }

        .left-controls {
            display: flex;
            gap: 20px;
            align-items: flex-start;
        }

        .joystick-container {
            width: 200px;
            height: 200px;
            background-color: #ddd;
            border-radius: 50%;
            position: relative;
            overflow: hidden;
        }

        .keyboard-box {
            width: 220px;
            padding: 12px;
            background-color: #fff;
            border-radius: 5px;
            border: 2px solid #333;
        }

        .keyboard-box h3 {
            margin-top: 0;
        }

        .keys-row {
            display: flex;
            justify-content: center;
            gap: 8px;
            margin: 8px 0;
        }

        .key {
            width: 48px;
            height: 48px;
            display: flex;
            align-items: center;
            justify-content: center;
            background: #e6e6e6;
            border: 2px solid #999;
            border-radius: 8px;
            font-weight: bold;
            font-size: 18px;
            user-select: none;
        }

        .key.active {
            background: #9dbdff;
            border-color: #4d7cff;
        }

        .keyboard-status {
            margin-top: 10px;
            font-size: 14px;
            color: #333;
        }

        .image-view {
            width: 640px;
            height: 480px;
            background-color: #000;
            border: 1px solid #333;
        }

        .position-display,
        .speed-controls {
            width: 220px;
            padding: 12px;
            background-color: #fff;
            border-radius: 5px;
        }

        .speed-controls h3,
        .position-display h3 {
            margin-top: 0;
        }

        .speed-controls label {
            display: block;
            margin-top: 10px;
            margin-bottom: 6px;
        }

        .speed-controls input[type="range"] {
            width: 100%;
        }

        .speed-value {
            font-weight: bold;
        }
    </style>
</head>

<body>
    <div class="connection-form">
        <input type="text" id="rosbridge-address" placeholder="ws://localhost:9090">
        <button onclick="connectToROS()">Connect</button>
    </div>

    <div class="dashboard">
        <div class="left-controls">
            <div class="joystick-container" id="joystick"></div>

            <div class="keyboard-box">
                <h3>WASD Control</h3>

                <div class="keys-row">
                    <div class="key" id="key-w">W</div>
                </div>
                <div class="keys-row">
                    <div class="key" id="key-a">A</div>
                    <div class="key" id="key-s">S</div>
                    <div class="key" id="key-d">D</div>
                </div>

                <div class="keyboard-status" id="keyboard-action">Current action: Stop</div>
                <div class="keyboard-status" id="pressed-keys">Pressed keys: none</div>
            </div>
        </div>

        <canvas class="image-view" id="camera-feed" width="640" height="480"></canvas>

        <div class="position-display">
            <h3>Position</h3>
            <p>X: <span id="position-x">0.00</span></p>
            <p>Y: <span id="position-y">0.00</span></p>
        </div>

        <div class="speed-controls">
            <h3>Speed Control</h3>

            <label for="linear-speed">
                Linear Speed:
                <span class="speed-value" id="linear-speed-value">0.50</span>
            </label>
            <input type="range" id="linear-speed" min="0.1" max="1.5" step="0.05" value="0.5">

            <label for="angular-speed">
                Angular Speed:
                <span class="speed-value" id="angular-speed-value">0.90</span>
            </label>
            <input type="range" id="angular-speed" min="0.1" max="2.0" step="0.05" value="0.9">
        </div>
    </div>

    <script>
        let ros;
        let cmdVelTopic;
        let joystick;
        let publishTimer = null;

        let joystickTwist = {
            linear: { x: 0, y: 0, z: 0 },
            angular: { x: 0, y: 0, z: 0 }
        };

        const keys = {
            w: false,
            a: false,
            s: false,
            d: false
        };

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
            cmdVelTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/cmd_vel',
                messageType: 'geometry_msgs/Twist'
            });

            const imageTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/camera/color/image_raw',
                messageType: 'sensor_msgs/Image'
            });

            imageTopic.subscribe(function (message) {
                const canvas = document.getElementById('camera-feed');
                const ctx = canvas.getContext('2d');

                canvas.width = message.width;
                canvas.height = message.height;

                const imageData = ctx.createImageData(message.width, message.height);
                const dst = imageData.data;

                let src = message.data;

                if (typeof src === 'string') {
                    const binary = atob(src);
                    const arr = new Uint8Array(binary.length);
                    for (let i = 0; i < binary.length; i++) {
                        arr[i] = binary.charCodeAt(i);
                    }
                    src = arr;
                } else if (Array.isArray(src)) {
                    src = Uint8Array.from(src);
                } else if (!(src instanceof Uint8Array)) {
                    src = new Uint8Array(src);
                }

                if (message.encoding === 'rgb8') {
                    for (let i = 0, j = 0; i < src.length; i += 3, j += 4) {
                        dst[j] = src[i];
                        dst[j + 1] = src[i + 1];
                        dst[j + 2] = src[i + 2];
                        dst[j + 3] = 255;
                    }
                } else if (message.encoding === 'bgr8') {
                    for (let i = 0, j = 0; i < src.length; i += 3, j += 4) {
                        dst[j] = src[i + 2];
                        dst[j + 1] = src[i + 1];
                        dst[j + 2] = src[i];
                        dst[j + 3] = 255;
                    }
                } else if (message.encoding === 'rgba8') {
                    for (let i = 0; i < src.length; i++) {
                        dst[i] = src[i];
                    }
                } else if (message.encoding === 'bgra8') {
                    for (let i = 0, j = 0; i < src.length; i += 4, j += 4) {
                        dst[j] = src[i + 2];
                        dst[j + 1] = src[i + 1];
                        dst[j + 2] = src[i];
                        dst[j + 3] = src[i + 3];
                    }
                } else if (message.encoding === 'mono8') {
                    for (let i = 0, j = 0; i < src.length; i += 1, j += 4) {
                        dst[j] = src[i];
                        dst[j + 1] = src[i];
                        dst[j + 2] = src[i];
                        dst[j + 3] = 255;
                    }
                } else {
                    console.error('Unsupported image encoding:', message.encoding);
                    return;
                }

                ctx.putImageData(imageData, 0, 0);
            });

            const odomTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/odom',
                messageType: 'nav_msgs/Odometry'
            });

            odomTopic.subscribe(function (message) {
                document.getElementById('position-x').textContent =
                    message.pose.pose.position.x.toFixed(2);
                document.getElementById('position-y').textContent =
                    message.pose.pose.position.y.toFixed(2);
            });

            setupSpeedControls();
            setupKeyboardControls();
            setupJoystick();
            startCmdVelLoop();
        }

        function setupSpeedControls() {
            const linearSlider = document.getElementById('linear-speed');
            const angularSlider = document.getElementById('angular-speed');
            const linearValue = document.getElementById('linear-speed-value');
            const angularValue = document.getElementById('angular-speed-value');

            linearValue.textContent = parseFloat(linearSlider.value).toFixed(2);
            angularValue.textContent = parseFloat(angularSlider.value).toFixed(2);

            linearSlider.addEventListener('input', function () {
                linearValue.textContent = parseFloat(this.value).toFixed(2);
            });

            angularSlider.addEventListener('input', function () {
                angularValue.textContent = parseFloat(this.value).toFixed(2);
            });
        }

        function getMaxLinearSpeed() {
            return parseFloat(document.getElementById('linear-speed').value);
        }

        function getMaxAngularSpeed() {
            return parseFloat(document.getElementById('angular-speed').value);
        }

        function isKeyboardActive() {
            return keys.w || keys.a || keys.s || keys.d;
        }

        function getKeyboardTwist() {
            const maxSpeed = getMaxLinearSpeed();
            const maxTurn = getMaxAngularSpeed();

            let linearX = 0;
            let angularZ = 0;

            const forward = keys.w;
            const backward = keys.s;
            const left = keys.a;
            const right = keys.d;

            if (forward && !backward) linearX = maxSpeed;
            if (backward && !forward) linearX = -maxSpeed;

            if (left && !right) angularZ = maxTurn;
            if (right && !left) angularZ = -maxTurn;

            return {
                linear: { x: linearX, y: 0, z: 0 },
                angular: { x: 0, y: 0, z: angularZ }
            };
        }

        function publishCurrentTwist() {
            if (!cmdVelTopic) return;

            const activeTwist = isKeyboardActive() ? getKeyboardTwist() : joystickTwist;

            const twist = new ROSLIB.Message({
                linear: {
                    x: activeTwist.linear.x,
                    y: 0,
                    z: 0
                },
                angular: {
                    x: 0,
                    y: 0,
                    z: activeTwist.angular.z
                }
            });

            cmdVelTopic.publish(twist);
        }

        function startCmdVelLoop() {
            if (publishTimer) {
                clearInterval(publishTimer);
            }

            publishTimer = setInterval(() => {
                publishCurrentTwist();
            }, 100);
        }

        function setupJoystick() {
            const options = {
                zone: document.getElementById('joystick'),
                mode: 'static',
                position: { left: '50%', top: '50%' },
                color: 'blue',
                size: 150
            };

            joystick = nipplejs.create(options);

            joystick.on('move', function (evt, data) {
                if (!data || !data.angle) return;

                const maxSpeed = getMaxLinearSpeed();
                const maxTurn = getMaxAngularSpeed();

                joystickTwist.linear.x =
                    Math.sin(data.angle.radian) * maxSpeed * (data.distance / 75);
                joystickTwist.angular.z =
                    -Math.cos(data.angle.radian) * maxTurn * (data.distance / 75);
            });

            joystick.on('end', function () {
                joystickTwist.linear.x = 0;
                joystickTwist.angular.z = 0;
                publishCurrentTwist();
            });
        }

        function updateKeyboardUI() {
            document.getElementById('key-w').classList.toggle('active', keys.w);
            document.getElementById('key-a').classList.toggle('active', keys.a);
            document.getElementById('key-s').classList.toggle('active', keys.s);
            document.getElementById('key-d').classList.toggle('active', keys.d);

            let action = 'Stop';

            const forward = keys.w;
            const backward = keys.s;
            const left = keys.a;
            const right = keys.d;

            if (forward && backward) {
                action = 'Brake / Stop';
            } else if (forward && left) {
                action = 'Forward-Left';
            } else if (forward && right) {
                action = 'Forward-Right';
            } else if (backward && left) {
                action = 'Backward-Left';
            } else if (backward && right) {
                action = 'Backward-Right';
            } else if (forward) {
                action = 'Forward';
            } else if (backward) {
                action = 'Backward';
            } else if (left && right) {
                action = 'Stop';
            } else if (left) {
                action = 'Turn Left';
            } else if (right) {
                action = 'Turn Right';
            }

            document.getElementById('keyboard-action').textContent = 'Current action: ' + action;

            const pressed = [];
            if (keys.w) pressed.push('W');
            if (keys.a) pressed.push('A');
            if (keys.s) pressed.push('S');
            if (keys.d) pressed.push('D');

            document.getElementById('pressed-keys').textContent =
                'Pressed keys: ' + (pressed.length ? pressed.join(', ') : 'none');
        }

        function setupKeyboardControls() {
            document.addEventListener('keydown', function (event) {
                const key = event.key.toLowerCase();

                if (key in keys) {
                    event.preventDefault();
                    keys[key] = true;
                    updateKeyboardUI();
                }
            });

            document.addEventListener('keyup', function (event) {
                const key = event.key.toLowerCase();

                if (key in keys) {
                    event.preventDefault();
                    keys[key] = false;
                    updateKeyboardUI();
                    publishCurrentTwist();
                }
            });

            updateKeyboardUI();
        }
    </script>
</body>

</html>
```
and you launch the server with : 
you go first to the folder : 
` cd ~/webpage_ws`
and then : 
`python3 -m http.server 7000`

this code supports a joystick and wasd control , a camera , coordinates x,y and a control panel for linear and angular speed . 
then you need to launch the camera with : 
source ofc : 
```
source /opt/ros/foxy/setup.bash
source install/setup.bash
```
and then : 
` ros2 launch orbbec_camera ob_camera.launch.py enable_color:=true enable_depth:=false enable_ir:=false ` 
this avtivates the normal camera . 

and thats  it , after that you go on the browser , and write : `http://172.20.43.207:7000/` and write this inside the block to connect : `ws://172.20.43.207:9090`
you can get the ip address with `hostname -I`

and that s it . next upgrade will be to try and make it not local (same wifi)


