---
layout: page
title:  Quadrotor Control and Trajectory Optimization
description: Tracking complex trajectories of Quadrotors using PID and optimal controllers like LQR and MPC
img: assets/img/quadrotor.jpg
importance: 3
category: Robot Control and RL
---

This project focuses on developing a controller for a quadrotor tasked with navigating restricted airspace while following a target position or trajectory. The quadrotor system consists of a frame with four propellers that generate lift and moments about the center of mass, allowing for precise position and orientation control. 

Github Link to the Project - [GitHub Repo](https://github.com/hitesh-vs/Quadrotor-PID-Control)

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/quadrotor.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Quadrotor navigating in the airspace following a target position/trajectory
</div>

The state of the system is defined by its position, velocity, and orientation (roll, pitch, yaw), with dynamics described using Newton-Euler equations as mentioned [here](https://ieeexplore.ieee.org/document/5569026). The objective of the controllers that are implemented is to follow two predefined trajectories in the 3D airspace, a diamond and a circular trajectory. Given the dynamics, the trajectories are tracked using two different controllers, the PD controller and an optimal controller (LQR), which are further explained next.

## PD Controller

To implement the PD controller, the property of **differential flatness** of the quadrotor system is used so as to make the control of the system easier. Through this property, we observe that even though the system has 12 state variables, we can simplify the control by only controlling the flat outputs of the system, which are $[x,y,z,\phi]^T$, the 3D position of the quadrotor and the yaw angle respectively. Using this, the controller can be simplified as shown: 

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/pd control.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Overview of the PD controller
</div>

The results of tracking using the PD controller are as follows. The Mean Square Error of the trajectories tracked are $0.012m^2$ and $0.027m^2$ for the diamond and the circle trajectories respectively.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/PD result path.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
     Tracking the Diamond and Circle Trajectories using PD controller
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/pd graph dia.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/circle pd graph.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Tracking Performance of the PD controller for the diamond trajectory (Left) and the circle trajectory (Right)
</div>

## LQR Controller

To implement the LQR, we first convert the system dynamics into a control affine form, after which the optimal control problem can be defined and solved. Similar to the PD controller, the LQR controller could track the trajectories effectively and the results are as follows:

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/traj lq.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
     Tracking the Diamond and Circle Trajectories using LQR controller
</div>

In the case fo the LQR controller, the Mean Square Error of the trajectories tracked are $0.035m^2$ and $0.064m^2$ for the diamond and the circle trajectories respectively.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/dia lq graph.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/circle lq graph.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Tracking Performance of the LQR controller for the diamond trajectory (Left) and the circle trajectory (Right)
</div>