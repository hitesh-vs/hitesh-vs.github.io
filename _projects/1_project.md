---
layout: page
title: Diffusion Models for Image Data Generation
description: An application of Denoising Diffusion Probabilistic Models (DDPM) to generate high-quality synthetic training data for an image classifier using the CIFAR-10 dataset.
img: assets/img/diffusion.png
importance: 2
category: Deep Learning and Computer Vision
#related_publications: true
---

In the age of Deep Learning, there is a huge need for data collection for efficiently training these models. but collecting high-quality datasets is often expensive, time-consuming, or even impractical. Synthetic data generation offers a solution to this problem—creating realistic, artificial data to train models effectively.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/Car1.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/Car2.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Collecting real-world data can be risky—foggy conditions, rare and unpredictable events, like accidents, are difficult to capture in datasets but crucial for training AI models.
</div>

This project discusses the implementation of a particular method of generation of artifical data - namely Diffusion Models. The aim of this project is to use Diffusion Models to generate high-quality synthetic training data for an image classifier using the CIFAR-10 dataset.

## Principle behind Diffusion Models

(Just a small and very brief 2 lines layman description of how the math works. I can add a pic too here if needed, guide me)

## Results 

- Some artificial images generated
- Comparision of Validation accuracy of the classsifier before and after addition of diffusion images.


Github Link to the project - [Link](https://github.com/hitesh-vs/Diffusion-DDPM)