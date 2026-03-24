Yes — here’s a short direct report/guide.

# Report — From SLAM Mapping to Saved Map and First AMCL Localization Attempt

## Goal

Move from:

* working LIMO diff simulation
* live SLAM mapping with `slam_toolbox`

to:

* saved static map
* first localization attempt using **AMCL**

---

## Starting point

The simulation was already working with:

* Gazebo
* RViz
* `/cmd_vel`
* `/odometry`
* `/scan`

Sim launch:

```bash
ros2 launch limo_description gazebo_models_diff.launch.py
```

Teleop:

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

SLAM:

```bash
ros2 launch slam_toolbox online_sync_launch.py use_sim_time:=true
```

RViz during mapping:

* Fixed Frame = `map`
* Map topic = `/map`
* LaserScan topic = `/scan`

---

## Step 1 — Build the map in SLAM

We manually explored a simple environment in Gazebo until the map looked usable in RViz.

Important rule:

* first map does not need to be perfect
* it just needs to be usable and stable

---

## Step 2 — Save the map

Create a folder first:

```bash
mkdir -p ~/limo_ros2_ws/maps
```

Save the map:

```bash
source /opt/ros/foxy/setup.bash
source ~/limo_ros2_ws/install/setup.bash
ros2 run nav2_map_server map_saver_cli -f ~/limo_ros2_ws/maps/my_map --ros-args -p map_subscribe_transient_local:=true
```

Generated files:

* `my_map.pgm`
* `my_map.yaml`

### Meaning of the files

* `.pgm` = grayscale occupancy map image
* `.yaml` = metadata file that tells ROS:

  * image name
  * resolution
  * origin
  * thresholds

---

## Step 3 — Important lesson about `slam_toolbox`

We clarified that:

* `slam_toolbox` was used to **create the map**
* the saved `.pgm` + `.yaml` are for **static map use**
* `slam_toolbox` localization mode does **not** directly use only this normal saved map pair the way AMCL does

So for localization on the saved map, we switched to:

**AMCL + map_server**

---

## Step 4 — Start localization with AMCL

Launch localization using the saved map:

```bash
source /opt/ros/foxy/setup.bash
source ~/limo_ros2_ws/install/setup.bash
ros2 launch nav2_bringup localization_launch.py map:=/home/salah/limo_ros2_ws/maps/my_map.yaml use_sim_time:=true
```

Important correction:

* the full correct path must be used
* `/home/limo_ros2_ws/...` was wrong
* `/home/salah/limo_ros2_ws/...` was correct

---

## Step 5 — What AMCL warning means

AMCL started successfully, but printed:

* cannot publish pose
* cannot update transform
* please set the initial pose

This means:

* map is loaded
* AMCL is running
* but it does not yet know where the robot is on the map

So AMCL needs an **initial pose estimate**.

---

## Step 6 — Initial pose concept

Important understanding:

The initial pose should be:

* **approximate**
* **not exact**
* **not random**

You estimate it by:

* looking at where the robot is in Gazebo
* matching that location to the same place on the map
* giving a rough heading direction

So the rule is:

**approximate and physically sensible, not exact and not random**

---

## Step 7 — RViz problem we found

The map was still not visible in RViz even though localization was running.

We checked:

```bash
ros2 topic info /map --verbose
```

This showed:

* `map_server` publishes `/map` with:

  * Reliability = `Reliable`
  * Durability = `Transient Local`

But RViz was subscribing with:

* Durability = `Volatile`

That was the reason RViz said:

* **No map received**

### Fix for RViz Map display

In RViz, for the **Map** display:

* Topic = `/map`
* Reliability Policy = `Reliable`
* Durability Policy = `Transient Local`

This was the key RViz fix.

---

## Step 8 — Displays needed in RViz

For localization, RViz should include:

* `Map`
* `LaserScan`
* `TF`
* `RobotModel`

Settings:

* Map topic = `/map`
* LaserScan topic = `/scan`

Fixed Frame:

* eventually `map`

---

## Step 9 — Useful terminal checks

Check map topic:

```bash
ros2 topic info /map --verbose
```

Check if AMCL is publishing pose:

```bash
ros2 topic echo /amcl_pose
```

---

## Main conclusions from today

* We successfully created and saved a map
* We understood the role of:

  * `my_map.pgm`
  * `my_map.yaml`
* We clarified the architecture:

  * `slam_toolbox` = mapping
  * `AMCL` = localization on saved map
  * `Nav2` = later goal-based navigation
* We discovered that RViz did not show the map because of a **QoS mismatch**
* We understood that AMCL needs an **initial pose estimate** at startup

---

## Correct architecture going forward

* **Block B:** use `slam_toolbox` to create map
* save map as `.pgm` + `.yaml`
* **Block C:** use `map_server + AMCL` for localization
* **Block D:** use Nav2 for goal-based navigation

---

## Next session

Resume with:

1. launch sim
2. launch AMCL localization with saved map
3. configure RViz Map display QoS correctly
4. set initial pose
5. verify scan aligns with map
6. test small motion
7. confirm `/amcl_pose`

If you want, I can turn this into an even cleaner **lab-note style checklist** you can reuse every time.
