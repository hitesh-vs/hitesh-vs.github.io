---
layout: page
title:  Quadrotor Control and Trajectory Optimization
description: Tracking complex trajectories of Quadrotors using PID and optimal controllers like LQR and MPC
img: assets/img/quadrotor.jpg
importance: 3
category: Robot Control and RL
---

This project focuses on developing a controller for a quadrotor tasked with navigating restricted airspace while following a target position or trajectory. The quadrotor system consists of a frame with four propellers that generate lift and moments about the center of mass, allowing for precise position and orientation control. [GitHub Repo](https://github.com/hitesh-vs/Quadrotor-PID-Control)

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/quadrotor.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Quadrotor navigating in the airspace following a target position/trajectory
</div>

The state of the system is defined by its position, velocity, and orientation (roll, pitch, yaw), with dynamics described using Newton-Euler equations as mentioned [here](https://ieeexplore.ieee.org/document/5569026). The objective of the controllers that are implemented is to follow two predefined trajectories in the 3D airspace, a diamond and a circular trajectory.




