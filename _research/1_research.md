---
layout: page
title: Dynamic Whole Body Push Recovery of Humanoid Robots while Walking
description: Developing Strategies for Humanoid Robots to recover from push distrubances during walking phase while stepping, along with posture regulation.
img: assets/img/push.png
importance: 2
category: Deep Learning and Computer Vision
#related_publications: true
---

## Research Problem

This research tackles an important problem for humanoid robots: how to keep walking when someone pushes them. Much like how humans can recover from unexpected bumps without falling over, we want robots to do the same.

When you're walking and someone pushes you, you instinctively adjust your steps and body posture to stay balanced. Our research gives robots the same ability by teaching them to:

Detect when they've been pushed

Decide how to respond based on the push strength and direction

Adjust their walking pattern to stay upright

Continue walking after recovering from the push

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/push.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

This is an ongoing work, but the idea is to use a High-Level MPC with footstep planner to detect and plan for the recovery steps after detecting a push while regulating posture.
   