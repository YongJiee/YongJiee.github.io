---
title: 'Autonomous Maze Explorer'
subtitle: 'ROS2, Nav2, SLAM, Python, Gazebo'
date: 2024-12-08 10:05:55 +0300
description: Developed an autonomous navigation system for a TurtleBot3 robot to explore and escape unknown mazes using SLAM Toolbox and Nav2 stack, achieving robust obstacle avoidance and intelligent exploration strategies.
featured_image: '/images/Projects/maze_explorer.png'
---

![](/images/Projects/maze_explorer_demo.jpg)

## Project Overview

This project demonstrates autonomous maze exploration and navigation using ROS2 (Robot Operating System 2). The TurtleBot3 robot autonomously navigates through unknown maze environments, builds a map in real-time using SLAM (Simultaneous Localization and Mapping), and successfully finds the exit without any pre-programmed map or path.

The system was developed as part of the RSE2108 ROS2 Assignment, where the robot had to solve mazes larger than 2.5m x 2.5m with grid sizes of 0.5m x 0.5m, starting from the center of the maze.

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

## System Architecture

The project uses a modular launch architecture with two main components. The simulation launch initializes the Gazebo environment, spawns the custom maze model from SDF files, launches the TurtleBot3 robot with precise positioning, and configures the robot state publisher for transforms. The navigation launch starts SLAM Toolbox with custom parameters for online mapping, launches Nav2 stack without a pre-existing map, configures RViz2 for visualization with custom view settings, and manages parameter files for different TurtleBot3 models.

The core exploration logic is implemented in a ROS2 node that subscribes to LiDAR and odometry topics, publishes velocity commands and navigation goals, interfaces with Nav2's action server, and implements state machine logic for exploration, navigation, and recovery. The node maintains multiple state flags to track navigation status, rotation state, infinite value detection, timeout conditions, and recovery mode.

---

## Key Challenges and Solutions

**Exit Recognition Challenge**: Distinguishing between temporary openings in the maze and the actual exit required robust detection logic. The solution implements a time-based confirmation system that requires 5 consecutive seconds of consistent infinite LiDAR readings across the forward hemisphere before declaring exit found. This prevents false positives from brief sensor anomalies or temporary openings.

**Navigation Failures Challenge**: The robot would occasionally get stuck in corners or narrow passages where Nav2's default recovery behaviors were insufficient. To solve this, I developed a multi-layered recovery system that analyzes local clearance, selects the safest escape direction, and executes controlled movements using direct velocity commands. The failure counter with cooldown prevents excessive recovery attempts while still allowing the system to self-correct.

**Real-Time Mapping Challenge**: Balancing SLAM accuracy with computational performance in unknown environments with tight corridors was critical. Custom SLAM parameters optimize the trade-off between map quality and processing speed. The online asynchronous mode allows continuous mapping without blocking navigation commands, ensuring responsive robot behavior even in complex environments.

---

## Results and Performance

The autonomous maze explorer successfully navigates complex maze environments, demonstrating several key capabilities. The robot explores unknown mazes without human intervention, building complete maps of the environment while searching for the exit. The 5-second confirmation window provides robust exit recognition with zero false positives in testing scenarios.

The multi-stage recovery system handles dead-ends, narrow passages, and navigation failures, with the robot successfully escaping stuck situations in over 95% of cases. SLAM Toolbox produces accurate, consistent maps suitable for path planning, with minimal drift even in larger maze configurations.

The project demonstrates proficiency in the ROS2 ecosystem, sensor processing and interpretation, autonomous navigation algorithms, state machine design, and integration of complex robotics frameworks.

---

## Code Highlights

The exploration algorithm demonstrates sophisticated sensor processing:

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
# Multi-directional clearance analysis
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
| Robot Platform | TurtleBot3 (Burger/Waffle) |
| Simulation | Gazebo with SDF models |
| Mapping | SLAM Toolbox (online async) |
| Navigation | Nav2 stack |
| Sensors | 360° LiDAR, wheel odometry |
| Language | Python 3 |
| Framework | ROS2 |
| Maze Size | > 2.5m × 2.5m (0.5m grid) |

---

## Future Enhancements

Potential improvements for the system include implementing more sophisticated exploration strategies such as Voronoi-based frontier detection, adding multi-robot coordination for faster maze solving, integrating machine learning for adaptive parameter tuning, and supporting 3D environments with stairs or ramps. These enhancements would further demonstrate the extensibility and scalability of the autonomous navigation architecture developed in this project.