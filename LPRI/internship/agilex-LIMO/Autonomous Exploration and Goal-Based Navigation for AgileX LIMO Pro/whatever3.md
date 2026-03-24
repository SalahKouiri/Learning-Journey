# Quick report — reaching today’s progress

## Goal reached today

You successfully completed **Block C**:

* loaded the saved map
* displayed it correctly in RViz
* fixed the QoS issue
* set the initial pose
* localized the robot with **AMCL**
* moved the robot and confirmed localization stayed correct

---

# Files already involved

Saved map files:

```bash
/home/salah/limo_ros2_ws/maps/my_map.yaml
/home/salah/limo_ros2_ws/maps/my_map.pgm
```

---

# Commands to run

## 1. Terminal 1 — launch the LIMO diff sim

```bash
source /opt/ros/foxy/setup.bash
source ~/limo_ros2_ws/install/setup.bash
ros2 launch limo_description gazebo_models_diff.launch.py
```

---

## 2. Terminal 2 — launch localization with map_server + AMCL

```bash
source /opt/ros/foxy/setup.bash
source ~/limo_ros2_ws/install/setup.bash
ros2 launch nav2_bringup localization_launch.py map:=/home/salah/limo_ros2_ws/maps/my_map.yaml use_sim_time:=true
```

This launches the localization stack using the saved map.

---

## 3. Terminal 3 — teleop, if needed

```bash
source /opt/ros/foxy/setup.bash
source ~/limo_ros2_ws/install/setup.bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

Use this only after localization is initialized, to test whether localization stays stable while moving.

---

## 4. Terminal 4 — optional checks

### Check `/map` topic exists

```bash
ros2 topic list | grep map
```

### Check `/map` QoS and subscribers

```bash
ros2 topic info /map -v
```

### Check `map_server` lifecycle state

```bash
ros2 lifecycle get /map_server
```

### Check AMCL pose

```bash
ros2 topic echo /amcl_pose
```

### Check `map -> odom` transform

```bash
ros2 run tf2_ros tf2_echo map odom
```

---

# RViz settings that needed to be changed

This was the key bug today.

## Fixed Frame

Set:

```text
map
```

## Add these displays

* Map
* LaserScan
* TF
* RobotModel

---

## Map display settings

This is where the important fix was.

Set the **Map** display to:

* Topic: `/map`
* Reliability Policy: `Reliable`
* Durability Policy: `Transient Local`

### Important mistake discovered today

Earlier, the durability setting was changed in the **wrong RViz display** like `RobotModel`.

That does **not** fix map display.

The QoS change must be done in the **Map display itself**.

---

## LaserScan display

Set:

* Topic: `/scan`

---

# What had to be done to finish localization

## 1. Make sure the map appears in RViz

The saved map did not appear at first because RViz Map display was subscribing with the wrong durability.

Correct fix:

* change **Map display** durability to **Transient Local**

---

## 2. Set the initial pose

In RViz:

* click **2D Pose Estimate**
* click approximately where the robot is on the map
* drag approximately in the robot’s facing direction

Important understanding:

* approximate is correct
* exact is not required
* random is wrong

---

## 3. Verify localization

After setting the initial pose:

* the robot localized correctly
* the robot could move
* localization remained consistent

That is why Block C is considered complete.

---

# Architecture confirmed today

## Mapping

Used earlier with:

```bash
ros2 launch slam_toolbox online_sync_launch.py use_sim_time:=true
```

Then map was saved as:

```bash
~/limo_ros2_ws/maps/my_map.yaml
~/limo_ros2_ws/maps/my_map.pgm
```

---

## Localization

Confirmed today as:

* `map_server` publishes saved map
* `AMCL` localizes on the saved map
* RViz must subscribe to `/map` with `Transient Local`

So the clean architecture is:

* `slam_toolbox` = mapping
* `.pgm + .yaml` = saved static map
* `map_server + AMCL` = localization on saved map

---

# Final status after today

## Completed

* Block A
* Block B
* Block C

## Next

* Block D: goal-based navigation with Nav2

---

# Best restart note for next session

Use this as your starting reminder:

**Block C is complete. Use `gazebo_models_diff.launch.py` for sim, `localization_launch.py map:=/home/salah/limo_ros2_ws/maps/my_map.yaml use_sim_time:=true` for localization, set RViz Fixed Frame to `map`, and in the Map display set `/map` with Durability = `Transient Local`. Then use 2D Pose Estimate to initialize AMCL.**

