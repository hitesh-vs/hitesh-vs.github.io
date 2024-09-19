---
title: "Trajectory Optimisation for Non-Prehensile Manipulation"
collection: research
type: "Undergraduate Thesis"
permalink: /research/Undergrad_thesis
venue: "Robotics Research Centre, IIIT Hyderabad"
date: Jul 2023 - Jan 2024
location: "Hyderabad, India"
---

Generated Jerk optimised trajectories for the xarm7 manipulator using Optimal Control for the Non-Prehensile manipulation task of transporting objects using a flat plate end-effctor.

![ExampleImage]({{ 'images/manipulator motion.png' | relative_url }})


During my Bachelor Thesis, I had the opportunity to explore the concepts of Optimal Coontrol and Trajectory Optimisation specifically in the case of Robot manipulators. This [report]() explains the assumptions and the evaluation of the work. The gist is explained here.

Abstract
======
This research addresses the challenge of generating optimal control trajectories for non-prehensile manipulation tasks, with a specific focus on transporting objects using a flat plate end-effector of the xArm7 manipulator. We formulated the problem as an optimal control problem employing direct collocation methods, aiming to minimize jerk while adhering to the dynamics of both the object and the manipulator as constraints. Through a trajectory optimization approach, we successfully generated jerk-minimized trajectories, achieving a significant reduction in total integrated jerk from 24.13 m/s² to 1.73 m/s². This represents a 92.8% decrease compared to traditional minimum time trajectories, highlighting the efficacy of our method in enhancing the performance of robotic manipulation tasks.


Simulation Setup
======
[Gazebo_Test]({{ 'images/2d present beer.mp4' | relative_url }})
[xarm7_Test]({{ 'images/2d present beer.mp4' | relative_url }})

The trajectories generated were verified through the physics simulator Gazebo to verify and correct the parameters in the problem setup. Initial simulation of the beer can on the flat plate end effector was done to estime the physical parameters like the friction coefficient between the object and the plate. The simulation showed that the trajectory adhered to the balancing constraints and the friction was estimated accurately.


Results
======
![ExampleImage]({{ 'images/hardware.png' | relative_url }})

The xArm7 manipulator provided by UFactory can be run directly through the xArm7 package
in ros using the UFactory Studio GUI. To run the hardware of the xArm7 manipulator through
a machine, firstly the UFactory Studio was installed in the machine. Then the machine
was connected to the physical manipulator via the ethernet. Once the robot is enabled, the
manipulator is ready to take commands