## Current Technical Issue

During the initial exploration of the **AgileX LIMO** software stack, the main difficulty was identifying the **actual control interface** used by the robot in the lab environment.

At first, the robot was tested through the **ROS2 Foxy workspace** (`limo_ros2_ws`) using the `limo_base` node and the `/cmd_vel` topic. The node was running correctly, `/cmd_vel` was receiving `Twist` commands, and `/odom` was available, but the robot still did not move. In parallel, the **official mobile app** was able to move the robot normally, which suggested that the app was either using a different control path or performing an additional initialization step that was not yet reproduced in the ROS2 setup.

Further investigation showed that the robot computer contains **multiple software stacks**, including a **ROS1 catkin workspace** (`agilex_ws`), a **ROS2 workspace** (`limo_ros2_ws`), and an additional **source repository** (`limo_ros`). This made the debugging process more complex because different commands, scripts, and packages belonged to different environments.

The documentation also introduced a separate **Python driver path** using the `pylimo` library, with helper scripts such as `limomove.py` and `limogetmessage.py`. This suggested that the robot may also support direct control through Python outside the ROS2 command path. However, this path required additional setup and verification, including installing the correct Python package version and adapting the script to the actual driver API.

In summary, the main problem was not simply “making the robot move,” but rather **determining which software path is the real working control interface for this specific LIMO setup**:
- ROS2 `/cmd_vel`
- ROS1 stack
- Python `pylimo` driver
- or a vendor-specific control interface used by the official app

This issue needs to be clarified before building the final web-based control dashboard.
