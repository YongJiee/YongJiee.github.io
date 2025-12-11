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

### Large 5x5 Maze
<video width="100%" controls >
    <source src="/images/Projects/Project4/Maze_02.mp4" type="video/mp4">
</video>

---

## Technical Implementation

### Core Technologies

The project integrates several sophisticated robotics frameworks and algorithms working in harmony. The Nav2 (Navigation 2) stack provides the core navigation capabilities, handling path planning, obstacle avoidance, and motion control with custom parameters tuned for optimal performance in tight maze corridors. SLAM Toolbox runs in online asynchronous mode to build the map while the robot explores, with custom mapper parameters that balance between mapping accuracy and computational efficiency for real-time performance.

LiDAR sensor data is continuously processed to detect walls, identify open spaces, and recognize the maze exit. The algorithm analyzes 360-degree laser scan data, filtering infinite values to identify potential exits and safe navigation paths with high precision.

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
| Robot Platform | TurtleBot3 (Burger) |
| Simulation | Gazebo with SDF models |
| Mapping | SLAM Toolbox (online async) |
| Navigation | Nav2 stack |
| Sensors | 360° LiDAR, wheel odometry |
| Language | Python 3 |
| Framework | ROS2 |
| Maze Size | > 2.5m × 2.5m (0.5m grid) |

---

### Maze Navigation Demonstrations
<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(320px, 1fr)); gap: 30px; margin: 40px 0;">
    <div style="text-align: center;">
        <img src="/images/Projects/Project4/Maze_final.gif" style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
        <p style="margin-top: 15px; font-weight: bold;">2.5x2.5m Maze</p>
        <p style="margin-top: 5px; color: #666; font-size: 0.9em;"></p>
    </div>
    <div style="text-align: center;">
        <img src="/images/Projects/Project4/Maze01_final.gif" style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
        <p style="margin-top: 15px; font-weight: bold;">Large 5x5m Maze</p>
        <p style="margin-top: 5px; color: #666; font-size: 0.9em;"></p>
    </div>
</div>

---

## Future Enhancements

Potential improvements for the system include implementing more sophisticated exploration strategies such as Voronoi-based frontier detection, adding multi-robot coordination for faster maze solving, integrating machine learning for adaptive parameter tuning, and supporting 3D environments with stairs or ramps. These enhancements would further demonstrate the extensibility and scalability of the autonomous navigation architecture developed in this project.