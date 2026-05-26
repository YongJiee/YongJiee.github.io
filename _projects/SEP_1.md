---
title: 'Autonomous Navigation with LIMO Robot using ROS1'
subtitle: 'ROS1 Melodic, Python, Navigation Stack, RTAB-Map'
date: 2025-05-05 12:00:00 +0000
description: Autonomous indoor navigation system for the AgileX LIMO robot — ROS1 Melodic, RTAB-Map SLAM, and a custom multi-stage recovery behavior for reliable path planning in tight spaces.
featured_image: '/images/Projects/SEP1/Final Navigation.gif'
---
<div class="tag-container">
  <span>ROS1</span>
  <span>RTAB-Map</span>
  <span>Navigation Stack</span>
  <span>Python</span>
  <span>RViz</span>
</div>

## Project Overview
As the **team leader**, I owned end-to-end software integration — connecting SLAM, the navigation stack, and recovery logic into a single working system. I directly programmed all navigation behavior in **Python and ROS1**, while teammates handled arena construction and hardware assembly.

🔗 [View Full Code & Report on GitHub →](https://github.com/YongJiee/Systems-Engineering-Project-1-Group-6.git)

<iframe src="https://www.youtube.com/embed/-_vPyRajMhM" width="640" height="360" frameborder="0" allowfullscreen></iframe>
<p style="font-style: italic; font-size: 0.9em; margin-top: 6px;">System overview & arena walkthrough</p>

---

## Project Highlights

- Led a **5-member team** as the sole programmer — teammates handled hardware assembly and arena construction
- Deployed autonomous indoor navigation on **AgileX LIMO Robot** using **ROS1 Navigation Stack**
- Used **RTAB-Map** for real-time **SLAM** — loop closure corrected drift mid-run
- **Implemented custom multi-stage recovery behavior** using `/move_base/make_plan` service
- Navigated **35cm-wide paths** in a **1.5m × 1.33m** arena with a **14cm-high ramp**

---

## Hardware Integration

<div style="display: flex; align-items: center; justify-content: space-between;">
  <!-- Hardware List -->
  <div style="width: 45%;">
    <h3 style="font-size: 1.6em;">Hardware Used:</h3>
    <ul style="font-size: 1.1em;">
      <li><strong>AgileX LIMO Robot</strong></li>
      <li><strong>Jetson Nano</strong></li>
      <li><strong>Orbbec Astra Depth Camera</strong></li>
      <li><strong>2D LiDAR Sensor</strong></li>
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

The navigation stack combines the following ROS components. RTAB-Map handles mapping and loop closure; AMCL then localizes the robot within that saved map during autonomous runs.

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

The hardest part wasn't getting the robot to navigate — it was getting it to recover when navigation broke. Costmap inflation, dead ends, and dynamic obstacles all caused path planning to fail mid-route.

**Solution:** Rather than failing immediately, the robot works through four escalating recovery stages before giving up:
- **Clears the local costmap** to remove stale obstacle data
- **Rotates in place** to scan for new paths
- **Reverses slightly** to escape dead ends
- **Aggressive costmap reset** as a last resort before failing

```python
def navigate_to_goal(self, plot_name, goal):
    self.state = RecoveryState.NAVIGATING

    if self.can_make_plan(goal):
        if self.send_goal(goal):
            return True

    for attempt in range(self.max_recovery_attempts):
        for recovery_behavior in self.recovery_behaviors:
            if recovery_behavior(goal):
                if self.can_make_plan(goal):
                    if self.send_goal(goal):
                        return True
                break
        rospy.sleep(1.0)

    self.state = RecoveryState.FAILED
    return False
```

If recovery still fails, the robot doesn't skip the plot and move on — it **returns to home first**, resets from a known-good position, then tries again. This stops one bad navigation from cascading into the rest of the route:

```python
def navigate_to_goal_with_retry(self, plot_name, goal):
    for attempt in range(self.max_goal_attempts):
        if self.navigate_to_goal(plot_name, goal):
            return True

    # Return to home, then retry from known-good position
    if not self.return_to_home():
        return False

    rospy.sleep(2.0)

    for attempt in range(self.max_home_retry_attempts):
        if self.navigate_to_goal(plot_name, goal):
            return True
        self.return_to_home()
        rospy.sleep(1.0)

    return False
```

In testing, this two-level retry loop eliminated the cascading failures we saw in early runs — a single bad goal no longer knocked out the rest of the route.

---

## Demonstration

Full autonomous run — the robot navigates all plots in sequence, with the multi-stage recovery behavior visible when it encounters a blocked path.

<iframe src="https://www.youtube.com/embed/YuCFiMQ4BtU" width="640" height="360" frameborder="0" allowfullscreen></iframe>
