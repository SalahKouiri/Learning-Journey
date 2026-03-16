# Autonomous Exploration and Goal-Based Navigation for AgileX LIMO Pro

This project extends the current **LIMO web dashboard** from a manual teleoperation interface into a more advanced **robot control center**.

The main goal is to enable the robot to:

- explore an indoor environment
- build a **2D occupancy map**
- localize itself on that map
- display the map and robot pose in the dashboard
- receive navigation goals from the dashboard
- move autonomously to the selected destination while avoiding obstacles

## Project Idea

The robot will first explore its environment and generate a map using **LiDAR** and **SLAM**.  
Once the map is built and saved, the robot will localize itself on that map and use **Nav2** to navigate autonomously to user-defined goals.

The dashboard will gradually evolve from a teleoperation page into a **full navigation interface**, where the user can monitor the robot, view the environment map, and later send goals directly from the map.

## Main Technologies

- **ROS 2**
- **AgileX LIMO Pro**
- **LiDAR**
- **SLAM Toolbox**
- **Nav2**
- **Gazebo**
- **HTML / CSS / JavaScript dashboard**
- **rosbridge / roslibjs**

## Workflow

### 1. Mapping
The robot explores the environment and builds a **2D map** using LiDAR and SLAM.

### 2. Localization
After the map is saved, the robot estimates its position on the map.

### 3. Navigation
The robot receives a target goal and uses **Nav2** for:

- path planning
- obstacle avoidance
- path following
- autonomous movement

### 4. Dashboard Integration
The dashboard will display:

- the map
- the robot pose
- the selected goal
- navigation status
- later, the planned path and sensor visualization

## Dashboard Evolution

### Current Features
- joystick control
- WASD control
- speed sliders
- live camera feed
- odometry display
- continuous `/cmd_vel` publishing

### Planned Features
- map visualization
- robot pose display
- coordinate input
- click-to-go navigation
- goal status
- path visualization
- LiDAR / obstacle visualization
- cancel goal / emergency stop

## Development Plan

### Phase 1 — Simulation
Start in **Gazebo** to test:

- LiDAR
- odometry
- SLAM
- map generation
- localization
- Nav2 goal navigation

### Phase 2 — Real Robot
Move the same pipeline to the real **LIMO**:

- build a real map
- save the map
- localize the robot
- test autonomous navigation

### Phase 3 — Dashboard Upgrade
Integrate navigation into the dashboard by adding:

- goal inputs
- goal sending
- navigation feedback
- later, map interaction

### Phase 4 — Remote Operation
Add remote access using:

- **4G SIM dongle**
- **Husarnet**

This will allow the robot to be monitored and controlled outside the lab network.

## Notes

- The first version focuses on **classical robotics navigation**, not reinforcement learning.
- **LiDAR** is the main sensor for the first implementation.
- Glass and transparent surfaces may create mapping issues for LiDAR.
- Reinforcement learning and AI-based navigation may be explored later as advanced extensions.

## Expected Outcome

At the end of this project, the LIMO should be able to:

- autonomously explore an environment
- create and use a 2D map
- localize itself
- receive goals from the dashboard
- navigate safely and autonomously to the selected destination

This project is a natural progression from manual robot control toward a more complete **autonomous mobile robot system**.
