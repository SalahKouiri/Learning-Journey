Here’s your quick revision report.

## RTAB-Map / Nav2 session report

### Goal

Try a better mapping pipeline than Cartographer in the fablab, because the old lidar map had too much gray/uncertain area and navigation became unstable.

---

## What we did

### 1. Started the real robot

Used the normal bringup:

```bash
ros2 launch limo_bringup limo_start.launch.py
```

### 2. Started the camera

Astra guide command did not fit your robot.
Your actual camera is **Orbbec DaBai**, so the working launch was:

```bash
ros2 launch orbbec_camera dabai.launch.py
```

Important finding:

* `ob_camera.launch.py` failed
* `dabai.launch.py` worked
* camera topics were published under `/camera/...`

### 3. Tried RTAB-Map

At first there was confusion because the guide used:

```bash
ros2 launch limo_bringup limo_rtab_rgbd.launch.py
```

but your installed package did **not** have that file.

Your actual installed RTAB launch files were:

* `limo_rtab_slam.launch.py`
* `rtabmap_rgbd_sync.launch.py`
* `limo_rtab_nav2_diff.launch.py`
* `limo_rtab_nav2_ackermann.launch.py`

So the correct mapping launch in your setup became:

```bash
ros2 launch limo_bringup limo_rtab_slam.launch.py
```

Important discovery:

* `limo_rtab_slam.launch.py` is just a wrapper
* it internally includes `rtabmap_rgbd_sync.launch.py`
* the real RTAB backend is inside that second file

### 4. Mapped with RTAB

Used teleop:

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

You drove the robot slowly and built a map.

Important observation:

* RTAB map looked better than the Cartographer map
* obstacles looked stronger/cleaner
* black walls/obstacles looked more solid than the old gray uncertain areas

### 5. Saved the RTAB database

After stopping RTAB, the DB existed:

```bash
~/.ros/rtabmap.db
```

It was around 145 MB, so the mapping session really produced a map database.

---

## Problems we encountered

### Problem 1: Wrong camera launch from the guide

The guide used Astra, but your robot uses Orbbec.

What happened:

* `astra_camera` path was wrong for your hardware
* `ob_camera.launch.py` also failed in your setup
* the working one was:

```bash
ros2 launch orbbec_camera dabai.launch.py
```

### Problem 2: RViz confusion with RTAB displays

You compared your RViz with a video and expected the same 3D behavior.

What happened:

* the vendor RTAB RViz config was very minimal
* it only had Grid, Image, Map, LaserScan, TF 
* it did **not** include the richer RTAB MapCloud setup from the video
* trying wrong topics made RViz crash

Important finding:

* `MapCloud` on `/cloud_map` was not the same as the RTAB plugin behavior in the video
* `DownloadMap` / `DownloadGraph` in your ROS 2 RTAB RViz plugin were effectively broken

The terminal literally showed:

```text
MapCloud plugin: DownloadMap still not working on ros2
```

So that button path was a dead end.

### Problem 3: RTAB backend ran, but 3D visualization path was misleading

The backend itself was alive:

* `/rtabmap`
* `/rgbd_sync`
* `/mapData`
* `/cloud_map`
* `/map`

But the RViz plugin behavior was not the same as the tutorial/video workflow.

Conclusion:

* backend may be fine
* RViz plugin path is unreliable in this ROS 2 setup

### Problem 4: Vendor RTAB → Nav2 integration failed

After mapping, you tried localization + navigation.

Used:

```bash
ros2 launch limo_bringup limo_rtab_slam.launch.py localization:=true
ros2 launch limo_bringup limo_rtab_nav2_diff.launch.py
```

Then you used:

* 2D Pose Estimate
* 2D Goal Pose

What happened:

* robot spun
* planner failed
* recoveries triggered
* global costmap failed

Important Nav2 errors:

* `Can't update static costmap layer, no map received`
* `The goal sent to the planner is off the global costmap`
* `Robot is out of bounds of the costmap`

This proved the problem was **not** just “goal sending.”
It was the RTAB → Nav2 map/global costmap integration.

### Problem 5: Runtime QoS / params mismatch

We checked `/map` in detail.

Important finding:

* `/map` was published by `rtabmap` as **TRANSIENT_LOCAL**
* `global_costmap` subscribed as **VOLATILE**

That mismatch explains why Nav2 kept saying no map received.

We also checked the live parameter:

```bash
ros2 param get /global_costmap/global_costmap static_layer.map_subscribe_transient_local
```

It printed:

```text
Boolean value is: False
```

even though the YAML file showed it as `True`.

That means there is some mismatch between:

* config file
* what gets loaded at runtime

### Problem 6: Stale topics in Nav2 RTAB params

Inside `nav2_rtab_diff.yaml`, the voxel layer still used an old RealSense topic:

```yaml
/intel_realsense_r200_depth/points
```

But your real point cloud topic is:

```text
/camera/depth/points
```

So the vendor RTAB Nav2 config is not well adapted to your current robot/camera stack.

---

## Commands we used

### Real robot start

```bash
ros2 launch limo_bringup limo_start.launch.py
```

### Working Orbbec camera launch

```bash
ros2 launch orbbec_camera dabai.launch.py
```

### RTAB mapping

```bash
ros2 launch limo_bringup limo_rtab_slam.launch.py
```

### Teleop

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

### Check RTAB database

```bash
ls -lh ~/.ros/rtabmap.db
```

### RTAB localization

```bash
ros2 launch limo_bringup limo_rtab_slam.launch.py localization:=true
```

### RTAB Nav2

```bash
ros2 launch limo_bringup limo_rtab_nav2_diff.launch.py
```

### Useful inspection commands

```bash
ros2 node list
ros2 topic list | grep camera
ros2 topic list | grep map
ros2 topic info /map -v
ros2 param get /global_costmap/global_costmap static_layer.map_subscribe_transient_local
ros2 run tf2_ros tf2_echo base_link laser_link
```

---

## Solutions we tried

### Tried

* wrong Astra camera path → rejected
* `ob_camera.launch.py` → failed
* `dabai.launch.py` → worked
* RTAB MapCloud topic experiments
* checking `/mapData`, `/cloud_map`, `/map`
* checking TF
* checking odom / scan / image topics
* trying RTAB localization + RTAB Nav2
* checking costmap QoS/runtime parameter mismatch
* checking launch files and YAML params directly

### What actually worked

* RTAB mapping itself
* Orbbec DaBai camera path
* producing a real RTAB database
* getting a visually better map than Cartographer

### What did not work

* vendor RTAB RViz plugin workflow
* vendor RTAB → Nav2 integration
* costmap/static map reception in Nav2

---

## Main conclusion

Your conclusion at the end was the right one:

> **RTAB mapping seems useful, but the shipped RTAB → Nav2 pipeline is broken/untrustworthy.**

So the best next practical strategy is:

### Preferred next strategy

1. use **RTAB only for mapping**
2. save a normal **2D occupancy map** from RTAB
3. then use the older known navigation pipeline:

```bash
ros2 launch limo_bringup limo_nav2.launch.py map:=/path/to/saved_map.yaml
```

Why this makes sense:

* Cartographer map was weak and gray/uncertain
* RTAB map looked better
* vendor RTAB navigation wrapper is broken
* so separate the problem into:

  * **RTAB for better mapping**
  * **classic Nav2 known-map navigation** for movement

---

## Final takeaway

Today’s real win was:

* you proved RTAB is more promising for your environment than lidar-only Cartographer
* you produced a valid RTAB database
* you discovered the vendor RTAB Nav2 integration is the weak part, not RTAB mapping itself

So the session was **not a failure**.
It gave a clear architectural conclusion:

> **Use RTAB as a mapping tool first. Don’t trust the vendor RTAB navigation wrapper yet.**

