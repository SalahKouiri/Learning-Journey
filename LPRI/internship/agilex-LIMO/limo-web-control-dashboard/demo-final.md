in `~/webpage_ws` , there is a logo.png which contains the lab logo used in the webpage . 
and there is the `index.html` which is the main code , here is the content : 
```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>LIMO Web Dashboard</title>

    <script src="https://cdn.jsdelivr.net/npm/roslib@1/build/roslib.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/nipplejs/0.9.0/nipplejs.min.js"></script>

    <style>
        * {
            box-sizing: border-box;
        }

        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 18px;
            background: #eef2f7;
            color: #1f2937;
        }

        .topbar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 20px;
            background: #ffffff;
            border-radius: 14px;
            padding: 16px 20px;
            margin-bottom: 20px;
            box-shadow: 0 4px 14px rgba(0, 0, 0, 0.08);
            flex-wrap: wrap;
        }

        .brand {
            display: flex;
            align-items: center;
            gap: 14px;
        }

        .logo {
            width: 70px;
            height: 70px;
            object-fit: contain;
            border-radius: 10px;
            background: #fff;
        }

        .brand-text .title {
            font-size: 24px;
            font-weight: bold;
            margin-bottom: 4px;
        }

        .brand-text .subtitle {
            font-size: 14px;
            color: #4b5563;
            margin-bottom: 2px;
        }

        .connection-panel {
            display: flex;
            flex-direction: column;
            gap: 8px;
            min-width: 300px;
        }

        .connection-row {
            display: flex;
            gap: 10px;
        }

        .connection-row input {
            flex: 1;
            padding: 10px 12px;
            border: 1px solid #cbd5e1;
            border-radius: 10px;
            font-size: 14px;
        }

        .connection-row button {
            padding: 10px 16px;
            border: none;
            border-radius: 10px;
            background: #2563eb;
            color: white;
            font-weight: bold;
            cursor: pointer;
        }

        .connection-row button:hover {
            background: #1d4ed8;
        }

        .status {
            font-size: 14px;
            font-weight: bold;
        }

        .status.connected {
            color: #15803d;
        }

        .status.disconnected {
            color: #6b7280;
        }

        .status.error {
            color: #dc2626;
        }

        .dashboard {
            display: grid;
            grid-template-columns: 470px 1fr 240px;
            gap: 20px;
            align-items: start;
        }

        .panel {
            background: #ffffff;
            border-radius: 14px;
            padding: 16px;
            box-shadow: 0 4px 14px rgba(0, 0, 0, 0.08);
        }

        .panel h3 {
            margin-top: 0;
            margin-bottom: 12px;
        }

        .left-panel {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .control-row {
            display: flex;
            gap: 20px;
            align-items: flex-start;
            flex-wrap: wrap;
        }

        .joystick-container {
            width: 200px;
            height: 200px;
            background: #dbe4f0;
            border-radius: 50%;
            position: relative;
            overflow: hidden;
            border: 2px solid #94a3b8;
        }

        .keyboard-box {
            width: 220px;
            padding: 12px;
            background: #f8fafc;
            border-radius: 12px;
            border: 2px solid #cbd5e1;
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
            background: #e5e7eb;
            border: 2px solid #94a3b8;
            border-radius: 10px;
            font-weight: bold;
            font-size: 18px;
            user-select: none;
        }

        .key.active {
            background: #93c5fd;
            border-color: #2563eb;
        }

        .keyboard-status {
            margin-top: 10px;
            font-size: 14px;
            color: #334155;
        }

        .speed-controls label {
            display: block;
            margin-top: 12px;
            margin-bottom: 6px;
            font-size: 14px;
        }

        .speed-controls input[type="range"] {
            width: 100%;
        }

        .speed-value {
            font-weight: bold;
        }

        .camera-panel {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .camera-title-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            gap: 12px;
            flex-wrap: wrap;
        }

        .camera-badge {
            background: #e0f2fe;
            color: #0369a1;
            padding: 6px 10px;
            border-radius: 999px;
            font-size: 13px;
            font-weight: bold;
        }

        .image-view {
            width: 100%;
            max-width: 900px;
            height: auto;
            aspect-ratio: 4 / 3;
            background: #000;
            border: 2px solid #1e293b;
            border-radius: 12px;
            display: block;
        }

        .right-panel {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .position-display p,
        .system-status p {
            margin: 8px 0;
            font-size: 15px;
        }

        .small-muted {
            font-size: 13px;
            color: #64748b;
        }

        @media (max-width: 1200px) {
            .dashboard {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>

<body>
    <div class="topbar">
        <div class="brand">
            <img src="logo.png" alt="LPRI Logo" class="logo" />
            <div class="brand-text">
                <div class="title">LIMO Web Dashboard</div>
                <div class="subtitle">Made by Kouiri Salaheddine</div>
                <div class="subtitle">Supervised by Ameur Soufiane</div>
            </div>
        </div>

        <div class="connection-panel">
            <div class="connection-row">
                <input type="text" id="rosbridge-address" placeholder="ws://localhost:9090" value="ws://localhost:9090" />
                <button onclick="connectToROS()">Connect</button>
            </div>
            <div id="connection-status" class="status disconnected">Status: Disconnected</div>
        </div>
    </div>

    <div class="dashboard">
        <div class="left-panel">
            <div class="panel">
                <h3>Manual Control</h3>
                <div class="control-row">
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
            </div>

            <div class="panel speed-controls">
                <h3>Speed Control</h3>

                <label for="linear-speed">
                    Linear Speed:
                    <span class="speed-value" id="linear-speed-value">0.50</span>
                </label>
                <input type="range" id="linear-speed" min="0.1" max="1.5" step="0.05" value="0.5" />

                <label for="angular-speed">
                    Angular Speed:
                    <span class="speed-value" id="angular-speed-value">0.90</span>
                </label>
                <input type="range" id="angular-speed" min="0.1" max="2.0" step="0.05" value="0.9" />
            </div>
        </div>

        <div class="panel camera-panel">
            <div class="camera-title-row">
                <h3>Live Camera Feed</h3>
                <div class="camera-badge">Compressed Stream</div>
            </div>
            <canvas class="image-view" id="camera-feed" width="640" height="480"></canvas>
            <div class="small-muted">
                Topic: /camera/color/image_raw/compressed
            </div>
        </div>

        <div class="right-panel">
            <div class="panel position-display">
                <h3>Position</h3>
                <p>X: <span id="position-x">0.00</span></p>
                <p>Y: <span id="position-y">0.00</span></p>
            </div>

            <div class="panel system-status">
                <h3>System Status</h3>
                <p>Control Source: <span id="control-source">Joystick</span></p>
                <p>Camera: <span id="camera-status">Waiting...</span></p>
            </div>
        </div>
    </div>

    <script>
        let ros;
        let cmdVelTopic;
        let joystick;
        let publishTimer = null;
        let imageTopic = null;
        let odomTopic = null;

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

        function setConnectionStatus(text, className) {
            const el = document.getElementById('connection-status');
            el.textContent = 'Status: ' + text;
            el.className = 'status ' + className;
        }

        function setCameraStatus(text) {
            document.getElementById('camera-status').textContent = text;
        }

        function updateControlSource() {
            document.getElementById('control-source').textContent =
                isKeyboardActive() ? 'Keyboard' : 'Joystick';
        }

        function connectToROS() {
            const rosbridgeAddress = document.getElementById('rosbridge-address').value.trim();

            if (!rosbridgeAddress) {
                alert('Please enter a rosbridge address.');
                return;
            }

            if (ros) {
                try {
                    ros.close();
                } catch (e) {
                    console.warn('Previous ROS connection close warning:', e);
                }
            }

            setConnectionStatus('Connecting...', 'disconnected');

            ros = new ROSLIB.Ros({
                url: rosbridgeAddress
            });

            ros.on('connection', function () {
                console.log('Connected to rosbridge server.');
                setConnectionStatus('Connected', 'connected');
                setupTopics();
            });

            ros.on('error', function (error) {
                console.error('Error connecting to rosbridge server:', error);
                setConnectionStatus('Error', 'error');
            });

            ros.on('close', function () {
                console.log('Connection to rosbridge server closed.');
                setConnectionStatus('Disconnected', 'disconnected');
            });
        }

        function setupTopics() {
            cmdVelTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/cmd_vel',
                messageType: 'geometry_msgs/Twist'
            });

            imageTopic = new ROSLIB.Topic({
                ros: ros,
                name: '/camera/color/image_raw/compressed',
                messageType: 'sensor_msgs/CompressedImage'
            });

            const canvas = document.getElementById('camera-feed');
            const ctx = canvas.getContext('2d');

            imageTopic.subscribe(function (message) {
                setCameraStatus('Streaming');

                const img = new Image();

                img.onload = function () {
                    ctx.clearRect(0, 0, canvas.width, canvas.height);
                    ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
                };

                img.onerror = function (err) {
                    console.error('Compressed image load error:', err);
                    setCameraStatus('Image Error');
                };

                img.src = 'data:image/jpeg;base64,' + message.data;
            });

            odomTopic = new ROSLIB.Topic({
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

            updateControlSource();
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

                let distanceFactor = data.distance / 75;
                if (distanceFactor > 1) distanceFactor = 1;

                joystickTwist.linear.x =
                    Math.sin(data.angle.radian) * maxSpeed * distanceFactor;
                joystickTwist.angular.z =
                    -Math.cos(data.angle.radian) * maxTurn * distanceFactor;

                updateControlSource();
            });

            joystick.on('end', function () {
                joystickTwist.linear.x = 0;
                joystickTwist.angular.z = 0;
                publishCurrentTwist();
                updateControlSource();
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

            updateControlSource();
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

and then for the the ` ~/start_limo_dashboard.sh ` , that starts all the processes here is its content : 
```
#!/bin/bash

ROBOT_WS="$HOME/limo_ros2_ws"
WEB_WS="$HOME/webpage_ws"

gnome-terminal --title="LIMO Bringup" -- bash -c "
cd '$ROBOT_WS'
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 launch limo_bringup limo_start.launch.py
exec bash
"

sleep 4

gnome-terminal --title="Rosbridge" -- bash -c "
cd '$ROBOT_WS'
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 launch rosbridge_server rosbridge_websocket_launch.xml
exec bash
"

sleep 3

gnome-terminal --title="Web Server" -- bash -c "
python3 -m http.server 7000 --directory '$WEB_WS'
exec bash
"

sleep 2

gnome-terminal --title="Orbbec Camera" -- bash -c "
cd '$ROBOT_WS'
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 launch orbbec_camera ob_camera.launch.py enable_color:=true enable_depth:=false enable_ir:=false
exec bash
"

sleep 4

gnome-terminal --title="Compressed Republisher" -- bash -c "
source /opt/ros/foxy/setup.bash
ros2 run image_transport republish raw compressed --ros-args --remap in:=/camera/color/image_raw --remap out/compressed:=/camera/color/image_raw/compressed
exec bash
"
```


that s it very simple stuff tho it took time debugging and finding out stuff since it s my first time working with ros 
