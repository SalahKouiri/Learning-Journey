# LIMO Nav2 + twist_mux Progress Report

## Date

April 1, 2026

## Goal of today

Set up the command-routing architecture needed before full Nav2 navigation:

* manual control -> `/cmd_vel_manual`
* Nav2 -> `/cmd_vel_nav`
* `twist_mux` output -> `/cmd_vel`
* robot base / Gazebo listens only to `/cmd_vel`

Also begin bringing up Nav2 localization and navigation on the saved map.

---

## What was completed today

### 1. `twist_mux` architecture was successfully established

We confirmed the correct command flow should be:

```text
teleop_twist_keyboard -> /cmd_vel_manual
Nav2                  -> /cmd_vel_nav
twist_mux             -> /cmd_vel
robot base            <- /cmd_vel
```

This is the architecture to keep going forward, including later on the real robot.

### 2. Manual control through `twist_mux` is working

`teleop_twist_keyboard` was remapped to publish to `/cmd_vel_manual`.

`twist_mux` was launched successfully and manual commands were confirmed to pass through to the final output.

The robot moved in Gazebo after fixing the `twist_mux` output remap.

### 3. Important `twist_mux` discovery

Your installed `twist_mux` version publishes to:

```text
/cmd_vel_out
```

by default, not directly to `/cmd_vel`.

So the working solution was to remap:

```text
/cmd_vel_out -> /cmd_vel
```

at launch time.

### 4. Nav2 bringup was partially started

Nav2 nodes launched successfully with:

* `controller_server`
* `planner_server`
* `recoveries_server`
* `bt_navigator`
* `waypoint_follower`
* lifecycle manager

This means the navigation stack itself is fundamentally runnable.

### 5. Localization issue was traced to the custom params file

A very important finding:

This command worked normally:

```bash
ros2 launch nav2_bringup localization_launch.py map:=/home/salah/limo_ros2_ws/maps/my_map.yaml use_sim_time:=true
```

But localization with the custom params file did not behave correctly at first.

This identified the problem as being in `~/limo_ros2_ws/config/nav2_params.yaml`, not in localization generally.

### 6. AMCL motion model fix

One key fix was made in the params file.

Changed from:

```yaml
robot_model_type: "differential"
```

to:

```yaml
robot_model_type: "nav2_amcl::DifferentialMotionModel"
```

After this change, localization behavior improved significantly and AMCL began working better.

---

## Files created or modified today

### 1. `~/limo_ros2_ws/config/twist_mux.yaml`

This file was created for `twist_mux` input-topic configuration.

Current working content:

```yaml
/**:
  ros__parameters:
    topics:
      manual:
        topic: /cmd_vel_manual
        timeout: 0.5
        priority: 100

      nav:
        topic: /cmd_vel_nav
        timeout: 0.5
        priority: 50
```

Notes:

* `manual` has higher priority than `nav`
* this means manual control overrides Nav2
* no explicit output topic is relied on in YAML because your installed `twist_mux` still published `/cmd_vel_out`

### 2. `~/limo_ros2_ws/config/nav2_params.yaml`

This file was copied from the built-in Nav2 example and then edited.

It was copied from:

```bash
/opt/ros/foxy/share/nav2_bringup/params/nav2_params.yaml
```

Important confirmed edits made:

#### Base frame consistency

All relevant frame references were made consistent with your robot:

```yaml
base_frame_id: "base_footprint"
robot_base_frame: base_footprint
```

This was corrected in all relevant sections.

#### Map path

Confirmed:

```yaml
yaml_filename: /home/salah/limo_ros2_ws/maps/my_map.yaml
```

#### Scan topic

Confirmed scan references were using `/scan` where relevant.

#### AMCL motion model

Changed to:

```yaml
robot_model_type: "nav2_amcl::DifferentialMotionModel"
```

### 3. No dashboard or real-robot file changes were part of this session

Today’s work was focused on the simulation-side Nav2 and `twist_mux` architecture.

---

## Exact commands that worked today

## A. Launch `twist_mux`

Working command:

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 run twist_mux twist_mux --ros-args --params-file ~/limo_ros2_ws/config/twist_mux.yaml -r /cmd_vel_out:=/cmd_vel
```

Why this exact command matters:

* your `twist_mux` publishes on `/cmd_vel_out`
* the remap makes the final robot command topic become `/cmd_vel`

---

## B. Launch manual teleop through the mux

Working command:

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args --remap cmd_vel:=/cmd_vel_manual
```

This publishes manual commands to `/cmd_vel_manual`.

---

## C. Useful debug commands for command routing

### See cmd_vel-related topics

```bash
ros2 topic list | grep cmd_vel
```

Expected topics:

```text
/cmd_vel
/cmd_vel_manual
/cmd_vel_nav
/cmd_vel_out
```

### Check what `twist_mux` is publishing and subscribing to

```bash
ros2 node info /twist_mux
```

### Check manual input topic

```bash
ros2 topic echo /cmd_vel_manual
```

### Check final robot command topic

```bash
ros2 topic echo /cmd_vel
```

### Check subscribers/publishers on final command

```bash
ros2 topic info /cmd_vel
```

---

## D. Launch localization without custom params

This worked normally:

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 launch nav2_bringup localization_launch.py map:=/home/salah/limo_ros2_ws/maps/my_map.yaml use_sim_time:=true
```

This is the fallback command you can trust if the custom params file causes trouble.

---

## E. Launch localization with custom params

Used for testing the custom config:

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 launch nav2_bringup localization_launch.py use_sim_time:=true map:=/home/salah/limo_ros2_ws/maps/my_map.yaml params_file:=/home/salah/limo_ros2_ws/config/nav2_params.yaml
```

Status:

* partially improved after the AMCL motion model fix
* not yet fully stable

---

## F. Launch navigation with custom params and remap Nav2 to `/cmd_vel_nav`

This was the launch command used for the navigation stack:

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 launch nav2_bringup navigation_launch.py use_sim_time:=true params_file:=/home/salah/limo_ros2_ws/config/nav2_params.yaml cmd_vel:=/cmd_vel_nav
```

Purpose:

* Nav2 command output goes to `/cmd_vel_nav`
* `twist_mux` arbitrates between `/cmd_vel_manual` and `/cmd_vel_nav`
* final output remains `/cmd_vel`

---

## G. RViz

Launch:

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
rviz2
```

Important RViz notes from today:

* `Fixed Frame = map` is the correct target for localization/navigation work
* if `map -> odom` is missing, the map and robot model will not coexist correctly
* changing QoS to `Transient Local` does not fix RobotModel TF issues

---

## Main technical findings from today

### 1. `twist_mux` output topic behavior

The installed `twist_mux` version uses `/cmd_vel_out` by default.

This is why the robot did not move at first even though:

* `/cmd_vel_manual` had messages
* `twist_mux` was alive

The final fix was:

```bash
-r /cmd_vel_out:=/cmd_vel
```

### 2. Manual path is verified

Confirmed working chain:

```text
teleop_twist_keyboard -> /cmd_vel_manual -> twist_mux -> /cmd_vel -> robot
```

### 3. Navigation stack launch itself is basically fine

When launched, Nav2 servers started correctly.

So the main remaining issue is not “Nav2 is broken”, but configuration/localization stability.

### 4. Localization failure was tied to params mismatch

The custom params file initially broke localization while the default no-params launch worked.

This is the key insight for tomorrow.

### 5. `robot_model_type` change was important

This line mattered:

```yaml
robot_model_type: "nav2_amcl::DifferentialMotionModel"
```

After correcting it, localization behavior improved.

---

## Current status at the end of today

## Confirmed working

### Working now

* `twist_mux` launches successfully
* manual teleop via `/cmd_vel_manual` works
* final `twist_mux` output reaches `/cmd_vel`
* Gazebo robot moves through the mux architecture
* default localization launch without custom params works
* custom params file has been improved
* Nav2 stack can launch

### Partially working / unstable

* localization with the custom big params file improved, but was not yet fully stable after motion

### Not yet fully finished

* stable AMCL localization using the custom big params file
* fully verified `map -> odom` persistence under motion with the custom big params file
* first successful goal navigation through `/cmd_vel_nav`
* full manual-over-Nav2 override test during active autonomous motion

---

## Current blocker

The current blocker is:

```text
Custom nav2_params.yaml still needs final AMCL/localization stabilization.
```

The default localization command works.
The custom file is close, but still needs final refinement so that localization remains stable after the robot moves.

---

## What to do tomorrow

## Recommended order

### Step 1 — start simulation / robot bringup as usual

Make sure Gazebo and the robot TF, odom, and scan are alive.

### Step 2 — launch `twist_mux`

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 run twist_mux twist_mux --ros-args --params-file ~/limo_ros2_ws/config/twist_mux.yaml -r /cmd_vel_out:=/cmd_vel
```

### Step 3 — verify manual teleop still works

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args --remap cmd_vel:=/cmd_vel_manual
```

Optional checks:

```bash
ros2 topic echo /cmd_vel_manual
ros2 topic echo /cmd_vel
ros2 topic info /cmd_vel
```

### Step 4 — test localization first

Start with the default working localization command:

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 launch nav2_bringup localization_launch.py map:=/home/salah/limo_ros2_ws/maps/my_map.yaml use_sim_time:=true
```

Confirm you can localize normally.

### Step 5 — compare with custom params localization

Then test:

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 launch nav2_bringup localization_launch.py use_sim_time:=true map:=/home/salah/limo_ros2_ws/maps/my_map.yaml params_file:=/home/salah/limo_ros2_ws/config/nav2_params.yaml
```

If this still becomes unstable after motion, focus only on AMCL-related parameters first.

### Step 6 — once localization is stable, launch navigation

```bash
cd ~/limo_ros2_ws
source /opt/ros/foxy/setup.bash
source install/setup.bash
ros2 launch nav2_bringup navigation_launch.py use_sim_time:=true params_file:=/home/salah/limo_ros2_ws/config/nav2_params.yaml cmd_vel:=/cmd_vel_nav
```

### Step 7 — verify Nav2 command routing

Check:

```bash
ros2 topic list | grep cmd_vel
ros2 topic info /cmd_vel_nav
ros2 topic info /cmd_vel
```

Goal state:

* Nav2 publishes on `/cmd_vel_nav`
* `twist_mux` publishes final output on `/cmd_vel`

### Step 8 — first real autonomous test

In RViz:

* Fixed Frame = `map`
* set initial pose
* send a short nearby goal

### Step 9 — test manual override over Nav2

While Nav2 is moving, use keyboard teleop.
Manual control should override autonomous motion immediately.

---

## Key permanent rules to keep for later, including the real robot

### Rule 1

Do not let manual control or Nav2 publish directly to `/cmd_vel`.

Use:

* manual -> `/cmd_vel_manual`
* Nav2 -> `/cmd_vel_nav`
* `twist_mux` -> `/cmd_vel`

### Rule 2

Always keep the `twist_mux` remap in your launch/command unless you later build a wrapper launch file:

```bash
-r /cmd_vel_out:=/cmd_vel
```

### Rule 3

For this robot, keep the base frame consistent as:

```yaml
base_footprint
```

### Rule 4

If localization works without the custom params file but breaks with it, do not debug the whole stack at once.
Start with AMCL parameters first.

---

## Summary in one sentence

Today’s real progress was that the full `twist_mux` command-routing architecture is now working in Gazebo, manual control through `/cmd_vel_manual` is verified, Nav2 can launch, and the remaining work is to stabilize AMCL/localization behavior in the custom `nav2_params.yaml` so navigation through `/cmd_vel_nav` can be tested cleanly tomorrow.
