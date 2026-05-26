---
title: 'Autonomous Maze Escape Robot'
subtitle: 'ROS2, Nav2, SLAM, Python, Gazebo'
date: 2025-09-01 12:00:00 +0000
description: A ROS2 robot that maps and escapes unknown mazes on its own — SLAM builds the map live, Nav2 handles the path, and a custom algorithm watches for the exit using an 80% LiDAR threshold.
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

**Individual project — sole developer** (ROS2, SLAM, Nav2, Python)

A TurtleBot3 drops into an unknown maze, maps it on the fly, and finds its own way out. No pre-programmed paths, no human input. Tested on mazes up to 5×5m — all in Gazebo simulation.

🔗 [View Full Code & Report on GitHub →](https://github.com/YongJiee/ROS2-Autonomous-exploration.git)

---

## Watch It In Action

<video width="100%" controls>
    <source src="/images/Projects/Project3/Maze_02.mp4" type="video/mp4">
</video>

*5×5m maze run — SLAM map building in real-time, recovery behavior triggered mid-run*

---

## What I Built

### Navigation System
- **Real-time SLAM mapping** — maps the environment on the fly as the robot moves
- **Autonomous path planning** — Nav2 figures out where to go and avoids obstacles
- **Exit detection algorithm** — LiDAR checks for an opening and confirms it for 5 seconds before stopping
- **Recovery behaviors** — scans all four directions and drives toward the clearest path when stuck

### System Architecture
- **Simulation (Gazebo)** — runs the robot in TurtleBot3 maze environments
- **Navigation Stack** — SLAM Toolbox + Nav2 + RViz for mapping and visualization
- **Exploration Node** — Python node I wrote for all the maze escape and recovery logic
- **Data Flow** — ROS2 topics wire together LiDAR, SLAM, navigation, and motor control

### Key Features
- **Dynamic goal generation** — goal position updates based on live sensor data
- **Time-confirmed exit detection** with **80% LiDAR threshold** held for 5 seconds
- **Recovery behaviors** tested and working in both maze sizes
- Tuned to handle corridors as narrow as **0.5m**

---

## Technical Implementation

### Core Technologies
- **ROS2** — middleware framework for the whole system
- **SLAM Toolbox** — builds the map in real-time as the robot moves
- **Nav2 Stack** — handles path planning and motor control
- **Python 3** — all the custom exploration and detection logic
- **Gazebo** — 3D physics simulation environment
- **LiDAR** — 360° laser scanner for obstacle and exit detection

### Programming Approach
- **Goal-directed exploration** — sets a far goal to drive exploration, swaps to the exit once detected
- **State machine** — switches between explore, detect, recover, and done modes
- **Time-based confirmation** — holds the exit check for 5 seconds before committing
- **Multi-stage recovery** — tries multiple directions before resuming exploration
- **Real-time sensor processing** — reads LiDAR every scan to update navigation decisions

---

## Key Challenges Solved

### Exit Recognition
**Challenge:** Telling apart an actual exit from a temporary gap in the wall  
**Solution:** If the front LiDAR stays above 80% open for 5 seconds straight, it's the exit. The 80% threshold was tuned empirically — lower values triggered false positives at wide corridor junctions; higher values missed valid exits near angled walls. Tested in both the 2.5×2.5m and 5×5m mazes — no false positives in either run.

### Navigation Failures
**Challenge:** Robot getting stuck in corners and dead-ends  
**Solution:** When stuck, the robot checks clearance in all four directions and drives toward whichever side has the most room.

### Real-Time Mapping
**Challenge:** Keeping the map accurate without slowing everything down  
**Solution:** Map updates every **0.5s** and kicks in on movements as small as **5cm** — tight enough to stay useful without tanking performance.

---

## Code Highlights

#### Exit detection — time-based confirmation
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

#### Multi-directional recovery system
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
- **Fully autonomous** — no waypoints, no hardcoded paths, just sensor data
- **Zero false positives** — exit detected correctly on the first run in both the **2.5×2.5m** and **5×5m** mazes
- **Recovery worked** — got unstuck and kept going in both configurations, no navigation failures
- Navigated successfully in both maze sizes without any manual intervention

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

I ran it on two different mazes to check that exit detection and recovery both hold up.

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(320px, 1fr)); gap: 30px; margin: 40px 0;">
    <div style="text-align: center;">
        <img src="/images/Projects/Project3/Maze_final.gif" style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
        <p style="margin-top: 15px; font-weight: bold;">2.5x2.5m Maze</p>
        <p style="margin-top: 5px; color: #666; font-size: 0.9em;">Smaller maze — first test, exit detection only</p>
    </div>
    <div style="text-align: center;">
        <img src="/images/Projects/Project3/Maze01_final.gif" style="width: 100%; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1);">
        <p style="margin-top: 15px; font-weight: bold;">Large 5x5m Maze</p>
        <p style="margin-top: 5px; color: #666; font-size: 0.9em;">Bigger maze — longer run, recovery triggered a few times</p>
    </div>
</div>

---
