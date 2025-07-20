---
title: 'ROS Navigation with LIMO robot'
subtitle: 'ROS1 Melodic, Python, Navigation Stack, RTAB-Map'
date: 2025-07-08 12:00:00 +0000
description: Developed an autonomous navigation system using ROS1 on the LIMO robot as part of a university group project. Integrated RTAB-Map for SLAM, configured the navigation stack, and enabled reliable indoor path planning in dynamic environments.
featured_image: '/images/Projects/SEP1/Navigation.gif'
---
<div class="tag-container">
  <span>ROS1</span>
  <span>RTAB-Map</span>
  <span>Navigation Stack</span>
  <span>Python</span>
  <span>RViz</span>
</div>
</div>

## Project Overview
<!--As the **team leader**, I was responsible for overall system integration and directly programmed the robot’s navigation behavior using **Python and ROS1**. -->

Our team applied the **SEBoK systems approach** to design and implement an autonomous navigation system as part of our university's **Systems Engineering Project 1** module.

We built a **1.5m x 1.33m arena** themed after Singapore Changi Airport’s Jewel Mastercard Bridge, featuring various navigation plots and a custom ramp structure. The **AgileX LIMO robot** navigated the arena autonomously using **ROS1 Melodic**, the **Navigation Stack**, and **RTAB-Map** for real-time **SLAM** and mapping.

🔗 [View Full Code & Report on GitHub →](https://github.com/YongJiee/Systems-Engineering-Project-1-Group-6.git)

<iframe src="https://www.youtube.com/embed/jBH--Sihadw" width="640" height="360" frameborder="0" allowfullscreen></iframe>

---

## Project Highlights

<!-- - Led a 5-member team and directly programmed the LIMO robot in ROS1 (Python) -->
- Developed autonomous indoor navigation using **ROS1 Navigation Stack**
- Integrated **RTAB-Map** for real-time **SLAM** and map generation
- Deployed on **AgileX LIMO Robot** with depth camera and LiDAR
- **Implemented custom recovery behavior** using `/move_base/make_plan` service
- Built a Changi Airport-themed arena with 35cm-wide paths and 14cm ramp

---

## Hardware Integration

<div style="display: flex; align-items: center; justify-content: space-between;">
  <!-- Hardware List -->
  <div style="width: 45%;">
    <h3 style="font-size: 1.6em;">Hardware Used:</h3>
    <ul style="font-size: 1.1em;">
      <li>AgileX LIMO Robot</li>
      <li>Jetson Nano</li>
      <li>Orrbec Astra Depth Camera</li>
      <li>2D LiDAR Sensor</li>
      <li>IMU (Inertial Measurement Unit)</li>
      <li>Wheel Encoders</li>
    </ul>
  </div>

  <!-- Image -->
  <figure style="width: 45%; text-align: center; font-style: italic;">
    <img src="/images/Projects/SEP1/Limo Robot.jpg" style="width: 85%;" class="img-fluid rounded">
    <p>AgileX LIMO Robot</p>
  </figure>
</div>

<div style="clear: both;"></div>

---
## Software Overview

<div style="display: flex; flex-wrap: wrap; gap: 16px; margin-top: 12px;">

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>SLAM</h4>
    <p><strong>Purpose:</strong> Real-time mapping and loop closure</p>
    <p><strong>Tool:</strong> <code>rtabmap_ros</code></p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Localization</h4>
    <p><strong>Purpose:</strong> Estimate robot pose in map</p>
    <p><strong>Tool:</strong> <code>amcl</code></p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Global Planning</h4>
    <p><strong>Purpose:</strong> Generate optimal path to goal</p>
    <p><strong>Tool:</strong> <code>NavFn</code> (inside <code>move_base</code>)</p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Local Planning</h4>
    <p><strong>Purpose:</strong> Navigate around local obstacles</p>
    <p><strong>Tool:</strong> <code>TrajectoryPlannerROS</code></p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Costmap</h4>
    <p><strong>Purpose:</strong> Create obstacle-aware 2D grid map</p>
    <p><strong>Tool:</strong> <code>costmap_2d</code></p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Recovery</h4>
    <p><strong>Purpose:</strong> Handle blocked paths or failed plans</p>
    <p><strong>Tool:</strong> Custom Python logic + costmap reset</p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Visualization</h4>
    <p><strong>Purpose:</strong> Visualize map, pose, and commands</p>
    <p><strong>Tool:</strong> <code>RViz</code></p>
  </div>

  <div style="flex: 1 1 280px; border: 1px solid #ddd; border-radius: 8px; padding: 12px; background: #f9fafb;">
    <h4>Command Control</h4>
    <p><strong>Purpose:</strong> Launch, topic/service control</p>
    <p><strong>Tool:</strong> <code>roslaunch</code>, custom Python nodes</p>
  </div>

</div>

---

## Arena Design & Physical Setup

**Drawing Layout:**
<img src="/images/Projects/SEP1/Draft_Final.jpg" style="width: 90%; margin-bottom: 10px; border-radius: 8px;">

**CAD Drawing & Real Plot View:**

<div style="display: flex; gap: 20px; margin-top: 10px;">
  <img src="/images/Projects/SEP1/Final_Drawing.png" style="width: 48%; border-radius: 8px;">
  <img src="/images/Projects/SEP1/Final_Plot.jpg" style="width: 48%; border-radius: 8px;">
</div>

---

## Technical Challenges & Solutions

One key challenge was handling situations where the robot could not generate a valid path due to costmap inflation, dead ends, or dynamic obstacles.  
**Solution:** We implemented a **custom recovery behavior** that:
- **Clears the local costmap**
- **Rotates in place** to scan for new paths
- **Reverses slightly** to escape dead ends  
 This greatly improved recovery success and allowed the robot to continue navigating reliably in dynamic environments.

---

## Development Timeline

- **Week 1:** Team formation, plot assignment, and setup of Ubuntu 18.04 development environment  
- **Week 2:** Learned core ROS1 concepts — packages, nodes, topics, and services  
- **Week 3:** Practiced using `roslaunch`, implemented custom publishers/subscribers, and built basic service/client 
- **Week 4:** Introduction to the ROS Navigation Stack — concepts like costmaps, TF, and robot footprint  
- **Week 5:** Navigation setup with LIMO robot, including `actionlib`, transforms, and real-world teleop testing  
- **Week 6:** Integrated RTAB-Map for SLAM using depth and LiDAR sensors in indoor environment  
- **Week 7:** Fine-tuned map resolution, visual SLAM performance, and robot localization using AMCL  
- **Week 8:** Implemented global and local planners with costmap tuning for tighter indoor spaces  
- **Week 9:** Developed custom recovery behavior — clear costmap, rotate, reverse, retry goal planning  
- **Week 10:** Conducted full-route testing and debugging in dynamic arena with obstacles and ramps  
- **Week 11:** Final system integration, edge case handling, and demo preparation  
- **Week 12:** Live demonstration of autonomous navigation


---

## Demonstration

This video showcases the LIMO robot performing full autonomous navigation in a custom-built indoor arena inspired by Changi Airport's Canopy Park. The robot uses **RTAB-Map** for real-time SLAM, along with the **ROS1 Navigation Stack** for path planning, obstacle avoidance, and dynamic recovery.

<iframe src="https://www.youtube.com/embed/1f2t4Yp_YZo" width="640" height="360" frameborder="0" allowfullscreen></iframe>
