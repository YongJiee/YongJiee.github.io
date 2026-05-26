---
title: 'Autonomous Maze Escape Robot'
subtitle: 'ROS2, Nav2, SLAM, Python, Gazebo'
date: 2025-09-01 12:00:00 +0000
description: Developed an autonomous navigation system for a TurtleBot3 robot to explore and escape unknown mazes using SLAM Toolbox and Nav2 stack, achieving time-confirmed exit detection with 80% LiDAR threshold and intelligent pathfinding.
featured_image: '/images/Projects/Project3/Exploration_Final.gif'
---

<div class="tag-container">
  <span>ROS2</span>
  <span>Nav2</span>
  <span>SLAM</span>
  <span>Python</span>
  <span>Gazebo</span>
</div>

## Project Overview

A TurtleBot3 explores an unknown maze, builds a map as it goes, and finds the exit — no pre-programmed paths, no human input. Built for the RSE2108 ROS2 Assignment across maze sizes up to 5×5m.
Everything runs in Gazebo simulation using physics-accurate SDF maze models.

🔗 [View Full Code & Report on GitHub →](https://github.com/YongJiee/ROS2-Autonomous-exploration.git)

---

## Watch It In Action

<video width="100%" controls>
    <source src="/images/Projects/Project3/Maze_02.mp4" type="video/mp4">
</video>

*TurtleBot3 navigating and escaping a 5×5m maze — real-time SLAM, no pre-programmed paths*

---

## What I Built

### Navigation System
- **Real-time SLAM mapping** - SLAM Toolbox builds maps while exploring
- **Autonomous path planning** - Nav2 stack handles obstacle avoidance
- **Exit detection algorithm** - LiDAR-based recognition with 5-second confirmation
- **Recovery behaviors** - Multi-directional clearance analysis and stuck detection

### System Architecture
- **Simulation (Gazebo)** - Physics simulation with custom SDF maze models
- **Navigation Stack** - SLAM Toolbox + Nav2 + RViz visualization
- **Exploration Node** - Custom Python logic for maze escape and recovery
- **Data Flow** - ROS2 topics connecting LiDAR, SLAM, navigation, and control

### Key Features
- Dynamic goal generation based on sensor data
- Time-confirmed exit detection with 80% LiDAR threshold
- Recovery behaviors validated across 2 maze configurations
- Parameters tuned for corridors down to 0.5m width

---

## Technical Implementation

### Core Technologies
- **ROS2** - Robot Operating System 2 framework
- **SLAM Toolbox** - Online asynchronous mapping
- **Nav2 Stack** - Path planning and motion control
- **Python 3** - Custom exploration algorithms
- **Gazebo** - 3D physics simulation
- **LiDAR** - 360° laser scanning for perception

### Programming Approach
- Frontier-based exploration algorithm
- State machine for navigation modes
- Time-based confirmation for exit detection
- Multi-stage recovery behaviors
- Real-time sensor processing

---

## Key Challenges Solved

### Exit Recognition
**Challenge:** Distinguishing actual exit from temporary openings  
**Solution:** Validated across both maze configurations — 2.5×2.5m and 5×5m — with zero false positives in either run. Recovery behaviors held up in both without any navigation failures.

### Navigation Failures
**Challenge:** Robot getting stuck in corners and dead-ends  
**Solution:** A custom recovery system checks clearance in all four directions and moves toward whichever has the most room.

### Real-Time Mapping
**Challenge:** Balancing SLAM accuracy with computational performance  
**Solution:** Parameters tuned to update the map every 0.5s and trigger on movement as small as 5cm — enough resolution without killing compute.

---

## Code Highlights

Exit detection with time-based confirmation:
```python
front_inf = sum(1 for i in inf_idx if 
               (a := math.degrees(msg.angle_min + i*msg.angle_increment)%360) >= 270 or a <= 90)
front_pct = (front_inf/total_front)*100 if total_front > 0 else 0

if front_pct > 80:
    if self.exit_start is None:
        self.exit_start = self.get_clock().now()
    elif (self.get_clock().now() - self.exit_start).nanoseconds/1e9 >= self.exit_time:
        self.maze_complete()
```

Multi-directional recovery system:
```python
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

## Results & Performance

### Capabilities Demonstrated
- Fully autonomous exploration - no waypoints, no pre-programmed paths
- Zero false positives across 2 maze configurations (2.5×2.5m and 5×5m), 1 run each
- Recovery behaviors executed successfully in both configurations with no navigation failures
- Successful navigation in 2.5m-5m+ mazes

### Technical Specifications

| Component | Technology |
|-----------|-----------|
| Robot Platform | TurtleBot3 Burger |
| Simulation | Gazebo with SDF models |
| Mapping | SLAM Toolbox (online async) |
| Navigation | Nav2 stack |
| Sensors | 360° LiDAR, wheel odometry |
| Language | Python 3 |
| Framework | ROS2 |

---

## Additional Maze Tests

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(320px, 1fr)); gap: 30px; margin: 40px 0;">
    <div style="text-align: center;">
        <img src="/images/Projects/Project3/Maze_final.gif" style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
        <p style="margin-top: 15px; font-weight: bold;">2.5x2.5m Maze</p>
        <p style="margin-top: 5px; color: #666; font-size: 0.9em;">Baseline test - autonomous exit detection</p>
    </div>
    <div style="text-align: center;">
        <img src="/images/Projects/Project3/Maze01_final.gif" style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
        <p style="margin-top: 15px; font-weight: bold;">Large 5x5m Maze</p>
        <p style="margin-top: 5px; color: #666; font-size: 0.9em;">Extended navigation with recovery behaviors</p>
    </div>
</div>

---