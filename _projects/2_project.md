---
layout: page
title: Adversarial Patch Generation for Monocular Depth Networks
description: Customised implementation of this paper by Yamanaka et. al. to attack Depth networks with Adversarial patches. 
img: assets/img/patch.jpg
importance: 2
category: Deep Learning and Computer Vision
giscus_comments: false
---

Github Link to the project - [Link](https://github.com/hitesh-vs/Adversarial-Attack-on-Neural-Nets-)

Deep learning models, particularly neural networks for **monocular depth estimation**, are susceptible to adversarial attacks. This project explores how adversarial patches can manipulate depth perception by fooling a **Depth Estimator Neural Network** into estimating incorrect depths.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/patch.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The goal is to train a patch that manipulates a depth estimator's output, forcing it to perceive incorrect depths in a targeted manner.
</div>

## Methodology

This project follows a structured approach to generate adversarial patches:

1. **Patch Augmentation:** Randomly initialized patches undergo transformations to mimic real-world variations.
2. **Patch Training:** Optimizing a loss function to **fool the depth estimator** into predicting incorrect depths.
3. **Real-World Testing:** Printing patches and testing their effectiveness on real-world images.

---

## Patch Training Process

Adversarial patches are trained using a loss function that consists of three major components:

1. **Depth Loss**: Forces a specific depth perception in the patch region.
2. **Non-Printability Score (NPS)**: Ensures printable colors.
3. **Total Variation (TV) Loss**: Smooths the patch texture for real-world use.

Mathematically, this is represented as:

$$
L = L_{depth} + \alpha L_{NPS} + \beta L_{TV}
$$

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/final patch.png" title="Patch Training Process" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Final patches obtanied after Training.
</div>

The pixel values of the patches are optimized through backpropagation until the optimal patch is found.

---

## 🖼️ Patch Application on Images

The trained adversarial patch is then applied to images, and its impact on depth estimation is evaluated.
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/10m.png" title="Patch Training Process" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/70m.png" title="Patch Training Process" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Results of the Depth Estimator Network for patches to mimic depths of 10m and 70m.
</div>


---

## 🌍 Real-World Testing

To test the effectiveness of adversarial patches outside of controlled environments, we **printed** the patches and placed them near objects in real-world scenes.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/Real Patch.png" title="Patch Training Process" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Real World Testing of the Obtained Patches.
</div>

Despite the additional errors due to the uneven camera motion, the patch successfully **altered depth predictions** for both the 10m and 70m distances.

---

## ⚡ Attack Using Fast Gradient Sign Method (FGSM)

Apart from adversarial patches, the **FGSM attack** was implemented to perturb input images adversarially.

\[
x_{adv} = x + \epsilon \cdot sign(\nabla_x L)
\]

This method provides a fast way to generate adversarial examples by maximizing the network’s error on a given input.
