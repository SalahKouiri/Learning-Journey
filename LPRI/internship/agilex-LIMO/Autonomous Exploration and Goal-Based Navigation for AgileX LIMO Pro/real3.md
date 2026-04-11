## GitHub Issue Summary: Nav2 & RTAB-Map Integration Debugging (Limo Pro)

**Environment:** ROS 2 Foxy, Agilex Limo Pro, RTAB-Map.

---

### **1. Accomplishments & Progress**
* **Node Hierarchy Fixed:** Successfully nested `global_costmap` and `local_costmap` parameters inside their respective server blocks (`planner_server` and `controller_server`) to match the Limo's specific launch structure.
* **QoS Durability Resolved:** Overcame the `volatile` vs `transient_local` mismatch. Both costmaps now correctly subscribe to the map with `transient_local` durability.
* **Global Map Alignment:** The global costmap is now successfully receiving the map from RTAB-Map and showing it in RViz.

---

### **2. Problems Encountered & Attempted Solutions**

#### **A. "Ghost" Nodes & Persistent Configs**
* **Problem:** Previous runs left background processes active, causing parameter edits to be ignored.
* **Solution:** Clean starts using `pkill -9 -f ros` before rebuilding.

#### **B. Local Costmap Drifting/Giant Scaling**
* **Problem:** Local costmap was trying to load the full static map, causing it to lose its "rolling window" behavior and drift off-center.
* **Solution:** Removed the `static_layer` from the `local_costmap` plugins and confirmed `rolling_window: true` with `global_frame: odom`.

#### **C. Phantom Obstacles (Self-Hits)**
* **Problem:** Flickering "white lines" appearing behind and in front of the robot (Lidar/Camera hitting the chassis).
* **Attempted Fix:** Increased `obstacle_min_range` to `0.25` and `min_obstacle_height` to `0.15`. 
* **Result:** Precision improved, but costmap behavior remained inconsistent.

#### **D. Frozen Local Costmap**
* **Problem:** **CRITICAL.** While raw `LaserScan` dots move in real-time in RViz during "hand tests," the purple/blue costmap bubbles stay fixed (frozen). 
* **Status:** The node appears to be hanging or dropping messages despite being in an `active` state.

---

### **3. What Worked vs. What Didn't**
* **WORKED:** Explicit YAML nesting inside server nodes.
* **WORKED:** Switching to `transient_local` for map subscription.
* **DID NOT WORK:** Wildcard (`/**`) parameter targeting.
* **DID NOT WORK:** Clearing services (`/clear_entirely...`) — services were unavailable/hanging due to node lifecycle issues.

---

### **4. Current Status**
The system is in a **"Partial Freeze"** state. The global map is visible, and raw sensor data is live, but the Nav2 Local Costmap is not updating its internal occupancy grid in response to new sensor data.

---

### **5. Next Steps**
1.  **Isolate the Sensor:** Launch with the **Minimal Lidar-only** configuration (removing `voxel_layer`) to see if the Orbbec camera data is flooding the node.
2.  **Lifecycle Debug:** Verify why costmap services are "waiting to become available" using `ros2 lifecycle get /controller_server`.
3.  **TF Check:** Validate the transform tree (`map -> odom -> base_link -> laser_link`) for high-frequency jitter or lag during movement.
4.  **Hand Test (Lidar-only):** Confirm if bubbles move when the camera is removed from the equation.



`ros2 launch limo_bringup limo_start.launch.py`
`ros2 launch orbbec_camera dabai.launch.py`
`ros2 launch limo_bringup limo_rtab_slam.launch.py localization:=true `
`ros2 launch limo_bringup limo_rtab_nav2_diff.launch.py `


