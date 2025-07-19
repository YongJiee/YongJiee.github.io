---
title: 'ROS Navigation with LIMO robot'
subtitle: 'ROS1 Melodic, Python, Navigation Stack, RTAB-Map'
date: 2025-07-08 12:00:00 +0000
description: Developed an autonomous navigation system using ROS1 on the LIMO robot as part of a university group project. Integrated RTAB-Map for SLAM, configured the navigation stack, and enabled reliable indoor path planning in dynamic environments.
featured_image: '/images/Projects/SEP1/Navigation.gif'
---

### Skills: ROS1 Navigation Stack, SLAM & Mapping, Python 

## Project Overview

As part of our university's Systems Engineering Project 1 module, our team applied the SEBoK systems approach to design and implement an autonomous navigation system. 
We built a 1.5m x 1.33m arena themed after Singapore Changi Airport’s Jewel Mastercard Bridge, featuring various navigation plots and a custom ramp structure. A LIMO robot was used to navigate the arena autonomously using **ROS1 Melodic**, the **Navigation Stack**, and **RTAB-Map** for real-time **SLAM** and mapping.

Check out the full program on [GitHub](https://github.com/YongJiee/Systems-Engineering-Project-1-Group-6.git).

<iframe src="https://www.youtube.com/embed/jBH--Sihadw" width="640" height="360" frameborder="0" allowfullscreen></iframe>

<!-- Hardware List on Left -->
<div style="width: 45%; float: left; margin-left: 0%; margin-right: 2%;">
  <h3>Hardware:</h3>
  <ul>
    <li>AgileX LIMO Robot</li>
    <li>Jetson Nano</li>
    <li>Orrbec Astra Depth Camera</li>
    <li>2D LiDAR Sensor</li>
    <li>IMU (Inertial Measurement Unit)</li>
    <li>Wheel Encoders</li>
  </ul>
</div>

<!-- Image on Right -->
<figure style="text-align: center; float: right; width: 50%; margin-left: 2%; margin-right: 0%; font-style: italic">
  <img src="/images/Projects/SEP1/Limo Robot.jpg" style="width: 70%;" class="img-fluid rounded">
  <p>AgileX LIMO Robot</p>
</figure>

<div style="clear: both;"></div>


### Final Layout
Drawing Layout:
<div class="gallery" data-columns="1">
  <img src="/images/Projects/SEP1/Draft_Final.jpg">
</div>
CAD Drawing:
<div class="gallery" data-columns="1">
  <img src="/images/Projects/SEP1/Final_Drawing.png">
</div>

Actual Plot View:
<div class="gallery" data-columns="1">
	<img src="/images/Projects/SEP1/Final_Plot.jpg">
</div>

### Final Demonstration Video
This video showcases the LIMO robot performing full autonomous navigation in a custom-built indoor arena inspired by Changi Airport's Canopy Park. The robot uses **RTAB-Map** for real-time SLAM, along with the **ROS1 Navigation Stack** for path planning, obstacle avoidance, and dynamic recovery.

<iframe src="https://www.youtube.com/embed/1f2t4Yp_YZo" width="640" height="360" frameborder="0" allowfullscreen></iframe>
