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

Built an autonomous navigation system where a TurtleBot3 robot explores unknown mazes, builds real-time maps using SLAM, and successfully finds the exit without any pre-programmed path. Developed for the RSE2108 ROS2 Assignment with maze sizes exceeding 2.5m × 2.5m.

🔗 [View Full Code & Report on GitHub →](https://github.com/YongJiee/ROS2-Autonomous-exploration.git)

---

## Watch It In Action

<video width="100%" controls>
    <source src="/images/Projects/Project4/Maze_02.mp4" type="video/mp4">
</video>

*TurtleBot3 successfully navigating and escaping a large 5x5m maze using real-time SLAM and intelligent pathfinding*

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
- 95%+ success rate escaping stuck situations
- Zero false positives on exit detection
- Custom tuned parameters for tight corridors

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
**Solution:** 5-second confirmation window requiring 80%+ forward infinite LiDAR readings

### Navigation Failures
**Challenge:** Robot getting stuck in corners and dead-ends  
**Solution:** Custom recovery system analyzing 4-direction clearance and executing safe movements

### Real-Time Mapping
**Challenge:** Balancing SLAM accuracy with computational performance  
**Solution:** Optimized parameters for 0.5s map updates and 5cm movement triggers

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
- Autonomous exploration without human intervention
- Real-time map building with minimal drift
- Robust exit detection (zero false positives)
- 95%+ recovery success rate
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

## Learning from This Project

This project provided hands-on experience integrating complex robotics frameworks and developing intelligent autonomous behaviors. The skills in ROS2, SLAM, and navigation planning developed here form a strong foundation for advanced robotics applications.

**Key Takeaways:**
- ROS2 ecosystem and multi-node architecture
- SLAM mapping and real-time localization
- Autonomous navigation and path planning
- Sensor fusion and data processing
- Robust error handling and recovery strategies