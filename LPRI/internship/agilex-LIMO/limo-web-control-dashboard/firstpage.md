### Web Control Interface Prototype

A simple web interface prototype was developed using **HTML, CSS, and JavaScript** to create a browser-based control page. The webpage captures **keyboard input (W, A, S, D)** and maintains the state of keys while they are held down, enabling continuous control rather than single key presses. The logic supports **simultaneous inputs** (e.g., forward + left) to allow more natural driving behavior. JavaScript event listeners detect `keydown` and `keyup` events, update the current control state, and display the resulting action on the page. This prototype represents the **first step toward a web-based teleoperation dashboard**, which will later be connected to a backend that communicates with the robot through **ROS2**.

---
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Driving Logic Test</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin-top: 80px;
      background-color: #f4f4f4;
    }

    h1 {
      margin-bottom: 20px;
    }

    .box {
      width: 400px;
      margin: 20px auto;
      padding: 20px;
      border: 2px solid #333;
      border-radius: 10px;
      background: white;
      font-size: 24px;
      font-weight: bold;
    }

    .small {
      font-size: 18px;
      margin-top: 10px;
      color: #444;
    }
  </style>
</head>
<body>
  <h1>Robot Driving Test</h1>

  <div class="box" id="output">Current action: Stop</div>
  <div class="small" id="keysDisplay">Pressed keys: none</div>

  <script>
    const output = document.getElementById("output");
    const keysDisplay = document.getElementById("keysDisplay");

    const keys = {
      w: false,
      a: false,
      s: false,
      d: false
    };

    function updateMovement() {
      let action = "Stop";

      const forward = keys.w;
      const backward = keys.s;
      const left = keys.a;
      const right = keys.d;

      if (forward && backward) {
        action = "Brake / Stop";
      } else if (forward && left) {
        action = "Forward-Left";
      } else if (forward && right) {
        action = "Forward-Right";
      } else if (backward && left) {
        action = "Backward-Left";
      } else if (backward && right) {
        action = "Backward-Right";
      } else if (forward) {
        action = "Forward";
      } else if (backward) {
        action = "Backward";
      } else if (left && right) {
        action = "Stop";
      } else if (left) {
        action = "Turn Left";
      } else if (right) {
        action = "Turn Right";
      }

      output.textContent = "Current action: " + action;

      const pressed = [];
      if (keys.w) pressed.push("W");
      if (keys.a) pressed.push("A");
      if (keys.s) pressed.push("S");
      if (keys.d) pressed.push("D");

      keysDisplay.textContent =
        "Pressed keys: " + (pressed.length ? pressed.join(", ") : "none");
    }

    document.addEventListener("keydown", function(event) {
      const key = event.key.toLowerCase();

      if (key in keys) {
        keys[key] = true;
        updateMovement();
      }
    });

    document.addEventListener("keyup", function(event) {
      const key = event.key.toLowerCase();

      if (key in keys) {
        keys[key] = false;
        updateMovement();
      }
    });
  </script>
</body>
</html>
```
---

![20260311-0255-59 0982958](https://github.com/user-attachments/assets/71ff9877-3846-49e7-9155-099fd2400b99)
