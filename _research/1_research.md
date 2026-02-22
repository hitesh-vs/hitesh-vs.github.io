---
layout: page
title: Graph Based Morphology Encoding for Robust Generalisation of RL Policies
description: RL policies need to be re-trained and fine tuned everytime when being tested on a new design. This research focusses on encoding methods to assist in application of generalised policies over a new real world robot design in simulation

img: assets/img/rl_research.png
importance: 2
category: Deep Learning and Computer Vision
#related_publications: true
---

## Research Problem

TThis research addresses the generalization of reinforcement learning (RL) policies across different robot morphologies, with the goal of reducing retraining when robot designs change.

• Problem: Existing RL policies must be retrained for new robot designs, limiting scalability and real-world deployment.

• Current Limitation: Transformer-based approaches such as MetaMorph enable cross-morphology training but do not explicitly encode structural connectivity, leading to weak transfer on complex, heterogeneous robots like the Unitree G1.

• Research Gap: Morphological encodings insufficiently capture graph-level relationships between links and joints, especially in underactuated or non-uniform real-world robots.

• Proposed Approach: Model robot URDFs as graphs and integrate graph-based encodings (GNN/GAT/GCNT) into Transformer actor–critic architectures trained with PPO.

• Objective: Improve structural awareness and inter-module communication to enable strong zero-shot generalization to unseen robot designs in simulation.