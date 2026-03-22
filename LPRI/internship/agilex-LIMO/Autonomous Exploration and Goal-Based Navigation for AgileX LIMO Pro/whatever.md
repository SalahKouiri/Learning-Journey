Absolutely. Here’s a quick **work report / restart sheet** for tomorrow.

---

# LIMO Autonomy Work Report — Current Status

## What was completed today

### Block A — Autonomy readiness

Confirmed working:

* Gazebo diff simulation launches
* RViz launches
* robot model visible
* TF active
* `/odometry` working
* `/scan` working
* teleop working
* robot moves correctly in RViz
* scan stays attached correctly
* no weird RViz motion behavior

Important confirmed frames:

* `/odometry`

  * `frame_id: odom`
  * `child_frame_id: base_footprint`
* `/scan`

  * `frame_id: laser_link`

Important RViz setup during validation:

* **Fixed Frame:** `odom`

Conclusion:

* **Block A passed**

---

### Block B — SLAM mapping

Completed:

* installed `slam_toolbox` for Foxy
* launched SLAM with simulation time
* confirmed SLAM node is running
* confirmed:

  * `/map`
  * `/map_metadata`
  * `/map_updates`
* confirmed `/map` has:

  * `frame_id: map`
* discovered empty-world issue
* created an environment in Gazebo
* map started working in RViz

Important lesson:

* empty Gazebo world = no meaningful map
* structured obstacles/walls are required for SLAM

Problem encountered:

* robot flips if collisions happen or if movement gets too aggressive
* map run becomes dirty after flips/crashes

Conclusion:

* **Block B is functionally proven**
* but first usable saved map is still pending
* tomorrow should use a **simple easy environment**, not a tight maze

---

# Commands used

## 1. Source ROS and workspace

Use these in new terminals when needed:

```bash
source /opt/ros/foxy/setup.bash
source ~/limo_ros2_ws/install/setup.bash
```

---

## 2. Launch LIMO Gazebo diff sim

```bash
source /opt/ros/foxy/setup.bash
source ~/limo_ros2_ws/install/setup.bash
ros2 launch limo_description gazebo_models_diff.launch.py
```

---

## 3. Keyboard teleop

```bash
source /opt/ros/foxy/setup.bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

Main keys:

* `i` forward
* `,` backward
* `j` rotate left
* `l` rotate right
* `k` stop

Speed adjustment:

* `q / z` increase or decrease all speeds
* `w / x` increase or decrease linear speed
* `e / c` increase or decrease angular speed

Use **slow speed** for mapping.

---

## 4. Install SLAM Toolbox

```bash
sudo apt update
sudo apt install ros-foxy-slam-toolbox
```

---

## 5. Launch SLAM Toolbox

```bash
source /opt/ros/foxy/setup.bash
ros2 launch slam_toolbox online_sync_launch.py use_sim_time:=true
```

---

## 6. Useful checks

### Check SLAM package

```bash
source /opt/ros/foxy/setup.bash
ros2 pkg list | grep slam_toolbox
```

### Check running nodes

```bash
source /opt/ros/foxy/setup.bash
ros2 node list
```

### Check SLAM sim time

```bash
source /opt/ros/foxy/setup.bash
ros2 param get /slam_toolbox use_sim_time
```

Expected:

```text
Boolean value is: True
```

### Check map topics

```bash
source /opt/ros/foxy/setup.bash
ros2 topic list | grep map
```

Expected:

* `/map`
* `/map_metadata`
* `/map_updates`

### Check odometry

```bash
source /opt/ros/foxy/setup.bash
ros2 topic echo /odometry
```

Important:

* `frame_id: odom`
* `child_frame_id: base_footprint`

### Check scan

```bash
source /opt/ros/foxy/setup.bash
ros2 topic echo /scan
```

Important:

* `frame_id: laser_link`

### Check map output

```bash
source /opt/ros/foxy/setup.bash
ros2 topic echo /map
```

---

# RViz setup

## During Block A validation

Use:

### Fixed Frame

```text
odom
```

### Displays to keep

* **TF**
* **RobotModel**
* **LaserScan**

### LaserScan topic

```text
/scan
```

Optional:

* Odometry display if needed, but it can clutter RViz badly

---

## During Block B mapping

Once SLAM Toolbox is running:

### Fixed Frame

```text
map
```

### Displays to keep

* **Map**
* **TF**
* **RobotModel**
* **LaserScan**

### Map topic

```text
/map
```

### LaserScan topic

```text
/scan
```

Important:

* do **not** use:

  * `/slam_toolbox/feedback`
  * `/slam_toolbox/scan_visualization`
  * `/slam_toolbox/update`

For the Map display, the correct topic is:

```text
/map
```

---

# Important things learned

## TF / frames

Confirmed important frame structure:

* `odom -> base_footprint`
* scan frame = `laser_link`
* `base_link` also exists
* wheel TF looked slightly suspicious in raw `/tf`, but not blocking current progress

## Empty world issue

If Gazebo is empty:

* scan mostly returns `.inf`
* SLAM has almost nothing to map
* `/map` may stay empty

## Flip issue

If robot flips:

* odometry / SLAM state becomes dirty
* current mapping run should be considered contaminated
* safest response is:

  * stop SLAM
  * reset / relaunch sim
  * start a new mapping session

## Mapping strategy

Do **not** try to build a hard full maze map right now.

Use:

* simple environment
* wide corridors
* wide turns
* few obstacles
* enough structure for LiDAR
* easy motion with low speed

Goal:

* **small usable map**
  not
* perfect full world coverage

---

# Recommended workflow for tomorrow

## Goal

Finish Block B properly by getting a **small usable saved map**.

## Plan

1. launch sim
2. launch teleop
3. launch SLAM Toolbox
4. use a **simple easy environment**
5. map slowly
6. stop once the map is usable
7. save the map
8. then move to Block C next

---

# Tomorrow’s exact start sequence

## Terminal 1 — Gazebo sim

```bash
source /opt/ros/foxy/setup.bash
source ~/limo_ros2_ws/install/setup.bash
ros2 launch limo_description gazebo_models_diff.launch.py
```

## Terminal 2 — Teleop

```bash
source /opt/ros/foxy/setup.bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

## Terminal 3 — SLAM

```bash
source /opt/ros/foxy/setup.bash
ros2 launch slam_toolbox online_sync_launch.py use_sim_time:=true
```

## RViz

Set:

* Fixed Frame = `map`
* Map topic = `/map`
* LaserScan topic = `/scan`

Keep displays:

* Map
* TF
* RobotModel
* LaserScan

---

# Stop point / continuation point

We completed:

* **Block A**
* **Block B functionally**

But tomorrow’s immediate job is:

## Finish Block B properly

* make an easy map
* avoid robot flipping
* save the map

Then after that:

## Next block

**Block C — localization on saved map**

---

If you want tomorrow, start by saying:
**“Continue from the end of Block B. Let’s build a small easy map and save it.”**
