---
title: 'ROS Navigation with LIMO robot'
subtitle: 'ROS1 Melodic, Python, Navigation Stack, RTAB-Map'
date: 2025-07-08 12:00:00 +0000
description: Developed an autonomous navigation system using ROS1 on the LIMO robot as part of a university group project. Integrated RTAB-Map for SLAM, configured the navigation stack, and enabled reliable indoor path planning in dynamic environments.
featured_image: '/images/Projects/SEP1/Navigation.gif'
---

### Skills: ROS1 Navigation Stack, SLAM & Mapping, Python

## 🧠 Project Overview

As part of our university's **Systems Engineering Project 1** module, our team applied the **SEBoK systems approach** to design and implement an autonomous navigation system.

We built a **1.5m x 1.33m arena** themed after Singapore Changi Airport’s Jewel Mastercard Bridge, featuring various navigation plots and a custom ramp structure. A LIMO robot was used to navigate the arena autonomously using **ROS1 Melodic**, the **Navigation Stack**, and **RTAB-Map** for real-time **SLAM** and mapping.

🔗 [View Full Code & Report on GitHub →](https://github.com/YongJiee/Systems-Engineering-Project-1-Group-6.git)

<iframe src="https://www.youtube.com/embed/jBH--Sihadw" width="640" height="360" frameborder="0" allowfullscreen></iframe>

---

## 🔍 Project Highlights

- 🚗 Developed autonomous indoor navigation using **ROS1 Navigation Stack**
- 🧠 Integrated **RTAB-Map** for real-time **SLAM** and map generation
- 🛠️ Deployed on **AgileX LIMO Robot** with depth camera and LiDAR
- 🧪 Constructed a Changi Airport-themed test arena with 35cm-wide paths and 14cm ramp
- ♻️ Implemented dynamic recovery logic using `/move_base/make_plan` service

---

## 🤖 Hardware Integration

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

## 🗺️ Arena Design & Physical Setup

**Drawing Layout:**
<img src="/images/Projects/SEP1/Draft_Final.jpg" style="width: 90%; margin-bottom: 10px; border-radius: 8px;">

**CAD Drawing & Real Plot View:**

<div style="display: flex; gap: 20px; margin-top: 10px;">
  <img src="/images/Projects/SEP1/Final_Drawing.png" style="width: 48%; border-radius: 8px;">
  <img src="/images/Projects/SEP1/Final_Plot.jpg" style="width: 48%; border-radius: 8px;">
</div>

---

## ⏱️ Development Timeline

- ✅ Week 1: Arena layout design & CAD modelling
- ✅ Week 2: Hardware setup & sensor testing (LiDAR + Depth camera)
- ✅ Week 3–4: ROS1 Navigation Stack + RTAB-Map integration
- ✅ Week 5: Recovery logic and path tuning
- ✅ Week 6: Testing, optimization, and demo recording

---

## 🎬 Demonstration

This video showcases the LIMO robot performing full autonomous navigation in a custom-built indoor arena inspired by Changi Airport's Canopy Park. The robot uses **RTAB-Map** for real-time SLAM, along with the **ROS1 Navigation Stack** for path planning, obstacle avoidance, and dynamic recovery.

<iframe src="https://www.youtube.com/embed/1f2t4Yp_YZo" width="640" height="360" frameborder="0" allowfullscreen></iframe>
