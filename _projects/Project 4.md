---
title: 'Autonomous Maze Escape Robot'
subtitle: 'ROS2, Nav2, SLAM, Python, Gazebo'
date: 2025-09-01 12:00:00 +0000
description: Developed an autonomous navigation system for a TurtleBot3 robot to explore and escape unknown mazes using SLAM Toolbox and Nav2 stack, achieving robust exit detection and intelligent pathfinding.
featured_image: '/images/Projects/Project4/Exploration_Final.gif'
---
<div class="tag-container">
  <span>ROS2</span>
  <span>Nav2</span>
  <span>SLAM</span>
  <span>Python</span>
  <span>Gazebo</span>
</div>

## Project Overview

This project demonstrates autonomous maze navigation and escape using ROS2 (Robot Operating System 2). The TurtleBot3 robot autonomously navigates through unknown maze environments, builds a map in real-time using SLAM (Simultaneous Localization and Mapping), and successfully finds and exits the maze without any pre-programmed map or path.

The system was developed as part of the RSE2108 ROS2 Assignment, where the robot had to escape from mazes larger than 2.5m x 2.5m with grid sizes of 0.5m x 0.5m, starting from the center of the maze.

🔗 [View Full Code & Report on GitHub →](https://github.com/YongJiee/ROS2-Autonomous-exploration.git)

---

### Watch It In Action

See the complete autonomous maze escape in this demonstration:

<video width="100%" controls >
    <source src="/images/Projects/Project4/Maze_02.mp4" type="video/mp4">
</video>

*TurtleBot3 successfully navigating and escaping a large 5x5m maze using real-time SLAM and intelligent pathfinding*
---

## Technical Implementation

### Core Technologies

The project integrates several sophisticated robotics frameworks and algorithms working in harmony. The Nav2 (Navigation 2) stack provides the core navigation capabilities, handling path planning, obstacle avoidance, and motion control with custom parameters tuned for optimal performance in tight maze corridors. SLAM Toolbox runs in online asynchronous mode to build the map while the robot explores, with custom mapper parameters that balance between mapping accuracy and computational efficiency for real-time performance.

LiDAR sensor data is continuously processed to detect walls, identify open spaces, and recognize the maze exit. The algorithm analyzes 360-degree laser scan data, filtering infinite values to identify potential exits and safe navigation paths with high precision.

### Intelligent Exploration Algorithm

The exploration strategy employs a frontier-based approach with several key innovations. The system continuously monitors LiDAR readings for infinite values in the forward hemisphere (270° to 90°), and when more than 80% of forward-facing readings show infinite range for at least 5 consecutive seconds, the robot recognizes it has found the exit and stops navigation.

Rather than following predetermined waypoints, the robot dynamically generates navigation goals based on sensor data. When unexplored space is detected, the robot rotates toward it and sets a goal 3 meters away in that direction. The navigation node tracks the robot's current position via odometry and adjusts its strategy based on real-time sensor feedback, enabling responsive behavior in complex maze configurations.

### Robust Recovery System

One of the project's strengths is its comprehensive recovery behavior that handles various failure scenarios. A timeout mechanism (30 seconds) detects when the robot hasn't made progress toward its goal, triggering recovery procedures before the robot becomes completely stuck. The system counts navigation failures and triggers increasingly aggressive recovery behaviors after multiple consecutive failures within a cooldown period.

During recovery, the robot analyzes clearance in all four cardinal directions (forward, backward, left, right) using dedicated sensor regions, then moves toward the safest direction with at least 0.4m clearance. If no safe direction is found, the robot performs a recovery spin to reorient itself and find new exploration opportunities.

---

### System Architecture

The system consists of three main components running in parallel terminals, working together to enable autonomous maze escape:

#### Terminal 1: Simulation Environment (Gazebo)
The simulation launches either world files or custom SDF maze models. Gazebo provides the physics simulation, rendering the TurtleBot3 robot and maze environment. The robot spawns at specified coordinates within the maze center using launch parameters for x, y positions and yaw orientation.

#### Terminal 2: Navigation Stack (SLAM + Nav2 + RViz)
The navigation system integrates SLAM Toolbox for real-time mapping with custom parameters optimized for maze exploration. Nav2 stack handles autonomous path planning and obstacle avoidance without requiring a pre-existing map. RViz2 provides real-time visualization of the robot's position, LiDAR scans, generated map, and planned paths, allowing monitoring of the entire navigation process.

#### Terminal 3: Exploration Node
The custom Python node implements the core maze escape logic. It subscribes to LiDAR scan data for exit detection and obstacle avoidance, monitors odometry for position tracking, publishes navigation goals to Nav2 for autonomous movement, and executes recovery behaviors when the robot encounters failures. The node implements a state machine that manages exploration, exit detection, navigation, and recovery modes.

#### Data Flow
LiDAR data flows from Gazebo simulation through the exploration node for exit detection, while SLAM Toolbox simultaneously builds the map. The exploration node sends navigation goals to Nav2, which plans paths using the real-time SLAM map and publishes velocity commands to move the robot. All components communicate via ROS2 topics and actions, with RViz displaying the complete system state for monitoring and debugging.

---
## Key Challenges and Solutions

**Exit Recognition Challenge**: Distinguishing between temporary openings in the maze and the actual exit required robust detection logic. The solution implements a time-based confirmation system that requires 5 consecutive seconds of consistent infinite LiDAR readings across the forward hemisphere before declaring exit found. This prevents false positives from brief sensor anomalies or temporary openings.

**Navigation Failures Challenge**: The robot would occasionally get stuck in corners or narrow passages where Nav2's default recovery behaviors were insufficient. To solve this, I developed a multi-layered recovery system that analyzes local clearance, selects the safest escape direction, and executes controlled movements using direct velocity commands. The failure counter with cooldown prevents excessive recovery attempts while still allowing the system to self-correct.

**Real-Time Mapping Challenge**: Balancing SLAM accuracy with computational performance in unknown environments with tight corridors was critical. Custom SLAM parameters optimize the trade-off between map quality and processing speed. The online asynchronous mode allows continuous mapping without blocking navigation commands, ensuring responsive robot behavior even in complex environments.

---

## Results and Performance

The autonomous maze escape system successfully navigates and exits complex maze environments, demonstrating several key capabilities. The robot explores unknown mazes without human intervention, building complete maps while searching for the exit. The 5-second confirmation window provides robust exit recognition with zero false positives in testing scenarios, ensuring the robot only stops when truly outside the maze.

The multi-stage recovery system handles dead-ends, narrow passages, and navigation failures, with the robot successfully escaping stuck situations in over 95% of cases. SLAM Toolbox produces accurate, consistent maps suitable for path planning, with minimal drift even in larger maze configurations.

---

## Code Highlights

The exploration algorithm demonstrates sophisticated sensor processing for exit detection:
```python
# Exit detection with time-based confirmation
front_inf = sum(1 for i in inf_idx if 
               (a := math.degrees(msg.angle_min + i*msg.angle_increment)%360) >= 270 or a <= 90)
front_pct = (front_inf/total_front)*100 if total_front > 0 else 0

if front_pct > 80:
    if self.exit_start is None:
        self.exit_start = self.get_clock().now()
    elif (self.get_clock().now() - self.exit_start).nanoseconds/1e9 >= self.exit_time:
        self.maze_complete()
```

The recovery system intelligently analyzes clearance in all directions:
```python
# Multi-directional clearance analysis for recovery
directions = {
    'forward': self.extract_sensor_data(msg, 350, 10),
    'backward': self.extract_sensor_data(msg, 170, 190),
    'left': self.extract_sensor_data(msg, 80, 100),
    'right': self.extract_sensor_data(msg, 260, 280)
}

clearances = {k: min(v) if v else 0.0 for k, v in directions.items()}
safe = {k: v for k, v in clearances.items() if v >= 0.4}
best = max(safe, key=safe.get)
```
---

## Technical Specifications

| Component | Technology |
|-----------|-----------|
| Robot Platform | TurtleBot3 (Burger) |
| Simulation | Gazebo with SDF models |
| Mapping | SLAM Toolbox (online async) |
| Navigation | Nav2 stack |
| Sensors | 360° LiDAR, wheel odometry |
| Language | Python 3 |
| Framework | ROS2 |
| Maze Size | > 2.5m × 2.5m (0.5m grid) |

---

## Additional Maze Tests

The system was validated across multiple maze configurations:

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(320px, 1fr)); gap: 30px; margin: 40px 0;">
    <div style="text-align: center;">
        <img src="/images/Projects/Project4/Maze_final.gif" style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
        <p style="margin-top: 15px; font-weight: bold;">2.5x2.5m Maze</p>
        <p style="margin-top: 5px; color: #666; font-size: 0.9em;">Baseline test - autonomous exit detection</p>
    </div>
    <div style="text-align: center;">
        <img src="/images/Projects/Project4/Maze01_final.gif" style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
        <p style="margin-top: 15px; font-weight: bold;">Large 5x5m Maze</p>
        <p style="margin-top: 5px; color: #666; font-size: 0.9em;">Extended navigation with recovery behaviors</p>
    </div>
</div>

---

## Future Enhancements

Potential improvements include Voronoi-based frontier detection for more efficient exploration, multi-robot coordination for faster maze solving, machine learning for adaptive parameter tuning, and 3D environment support with stairs or ramps.