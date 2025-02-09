---
layout: page
title: High to Low Level Task Planning and Execution using RL
description: An RL framework for mobile manipulators to learn navigation and grasping tasks sequentially for Household tasks
img: assets/img/RLimage.png
importance: 3
category: Robot Control and Navigation
---

This project presents a framework for mobile manipulators using Hierarchical Reinforcement Learning (HRL) and Reward Shaping to tackle complex tasks efficiently. Intrinsic Curiosity fosters self-driven exploration, while Unity ML Agents enable a proof-of-concept for navigation and object manipulation in unknown environments. Future work aims to integrate large language models (LLMs) for task decomposition and enhanced automation.

The specific problem we aim to solve through this project is for a **mobile manipulator to learn to solve the problem of cleaning a table in a room autonomously**. For this purpose, the robot first needs to navigate to the location of the table in the room, then pick up the trash on the table and then navigate to the location of the trash can. We carried out the implementation in three phases:

1. Starting from the low level tasks, we created an RL agent to learn to navigate to a target location avoiding obstacles
2. After Reaching the location, another agent is created to learn to pick the trash
3. Once the agents for navigation and picking have learnt the optimal policies, they are integrated through the concepts of Heirarchical RL.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RLimg.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Visualisation of the entire pipeline of task planning and execution in Unity MLAgents environment. Here the orange boxes are obstacles and the green cylinder is the trash. The green cube at the end is the final location of the trash can to which the robot needs to navigate.
</div>


## Enhancing Navigation using Curriculum Learning

Our initial navigation task trained the robot in a large room using a sparse reward function (+1 for reaching the target, -1 for collisions). This led to suboptimal behavior, such as avoiding movement to escape penalties. Switching to a dense reward function improved training but failed to generalize due to the environment's complexity.

To address this, we implemented Curriculum Learning, starting with a simple environment (fixed target, no obstacles) and gradually increasing difficulty. The agent, trained using PPO, leveraged prior models for each stage, ensuring steady progress.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/NavPic.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/RandomPic.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/ObsPic.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The process of Curriculum Learning for the navigation task. The agent was first trained to navigate to a fixed target, then to a target with random locations and finally to navigate through the scene with obstacles.
</div>

In the simple setup, the agent achieved an average reward of 0.997 after 140,000 episodes. This structured training approach enabled better navigation in complex environments with obstacles.


## Improving the Pick Task with Curiosity-Driven Learning

A 2-DOF manipulator was trained to touch a target on a tabletop. Initial training with sparse rewards (+1 for success) failed due to the large state space and lack of feedback. Reward shaping was introduced, penalizing collisions and rewarding proximity, improving learning but yielding suboptimal policies.

To address this, the Intrinsic Curiosity Module (ICM) was added, encouraging exploration by providing rewards for discovering unexplored states. This curiosity-driven approach helped the agent refine its policy and achieve more efficient task performance.


## Approaches for Task Planning

Three approaches were explored for robot task planning:

* Task Planning using LLMs: Train large language models (LLMs) to break down high-level commands into actionable robot sequences (e.g., Plan-Seq-Learn, SayCan).

* Using Behavior Trees: Employ a framework to decide when to switch actions and determine required actions for low-level task execution.

* Using Hierarchical RL: Use a high-level policy to sequence subgoals and a low-level policy to learn individual subtasks.

A combination of Hierarchical RL and Behavior Trees was used to enable sequential task execution, ensuring smooth transitions and successful task completion.

The complete implementation of the above phases and the results we obtained are depicted in this presentation : [Link](https://rltaskplanner.my.canva.site/plan)

