---
layout: page
title: Semantic and Instance Segmentation of Aerial Drone Imagery
description: A custom CNN model inspired by ResNet-18 that can perform Semantics on footage obtained from drones.
img: assets/img/Segmentation.jpg
#redirect: https://unsplash.com
importance: 2
category: Deep Learning and Computer Vision
---

Github Link to the project - [Link](https://github.com/hitesh-vs/Semantic-and-Instance-Seg)

Accurate **Semantic and Instance segmentation** is critical for **autonomous drone navigation**, especially when maneuvering through obstacles like racing windows. Deep Learning models such as a simple U-Net can be trained for executing segmentation tasks. Further, models like Mask R-CNNs can be used for further tasks like Object detection and Instance Segmentation.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/Segmentation.png" title="Synthetic Data Generation" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Using UNet and Connected Component Analysis, Semantic and Instance Segmentation were performed on drone racing windows.
</div>

---

## Project Overview

This project explores **Semantic and Instance segmentation** for **drone perception** tasks. It follows three key stages:

1. **Dataset Generation:**  
   - Use **Blender** to create images with various lighting, backgrounds, and occlusions.
   - Generate **segmentation masks** for training.  

2. **Semantic Segmentation:**  
   - Implement **U-Net with a MobileNet encoder** for object segmentation.

3. **Instance Segmentation:**  
   - Apply **connected component analysis** to distinguish multiple objects.

---

## Dataset Generation

Since manually collecting data is impractical, we **generated** images using **Blender** with **domain randomization** to create realistic training data.

- **Different Object Orientations**
- **Lighting & Background Variations**
- **Occlusion Handling**

Each generated image includes a **corresponding ground truth segmentation mask** for training.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/drone data gen.png" title="Synthetic Data Generation" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Image of a scene generated along with its Ground truth segmentation mask, both of which are generated through Blender.
</div>

---

## Data Augmentation

To improve generalization, **data augmentation** techniques were applied:

| Transformation | Example |
|---------------|---------|
| **Camera Angle Change** | ![Aug 1](assets/img/camera tilt.png) |
| **Background Variation** | ![Aug 2](assets/img/bg change (2).png) |
| **Lighting Change** | ![Aug 3](assets/img/lighting change.png) |
| **Noise & Blur** | ![Aug 4](assets/img/image (2).png) |
| **Color Transformation** | ![Aug 5](assets/img/clr jitter.png) |

Augmentations were implemented using **PyTorch's torchvision.transforms**.

---

## Semantic Segmentation Model

We trained a **U-Net-based model** with a **MobileNet encoder** for semantic segmentation.

### **Architecture Overview**
- **Encoder:** Uses a ResNet-like structure with **convolutional layers** and **skip connections**.
- **Decoder:** Upsamples features using **transposed convolutions**.
- **Final Layer:** Produces a **binary segmentation mask**.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/unet_architecture.png" title="U-Net Architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    U-Net architecture used for semantic segmentation.
</div>

### **Loss Function**
The **Binary Cross-Entropy (BCE) Loss** was used for pixel-wise classification:

$$
L_{BCE} = - \frac{1}{N} \sum_{i=1}^{N} [ y_i \log(p_i) + (1 - y_i) \log(1 - p_i) ]
$$

where:
- **\(y_i\)** is the ground truth label (0 or 1).
- **\(p_i\)** is the predicted probability.

---

## 🔍 Instance Segmentation

Instance segmentation aims to distinguish multiple **overlapping objects**. We used the **Connected Components Algorithm** to assign unique labels to each object.

### **Algorithm Steps**
1. **Input:** Binary segmentation mask.
2. **Identify Connected Regions:** Assign unique labels to each cluster.
3. **Use 4-connectivity or 8-connectivity** to group pixels into objects.

| Semantic Segmentation | Instance Segmentation |
|----------------------|----------------------|
| ![Semantic](assets/img/semantic_mask.png) | ![Instance](assets/img/instance_mask.png) |

---

## 🏆 Experiments & Results

### **Training Hyperparameters**
| Hyperparameter | Value |
|---------------|------|
| **Epochs** | 100 |
| **Batch Size** | 32 |
| **Total Images** | 40,000 |
| **Learning Rate** | 1e-4 |
| **Optimizer** | ADAM |

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/training_loss.png" title="Training & Validation Loss" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Loss curve showing model convergence during training.
</div>

---

### **Failure Cases**
1. **Tilted Windows**  
   - The model struggled to segment windows when **heavily tilted**, likely due to a **lack of diverse training data**.

2. **Small Windows on Dark Backgrounds**  
   - Objects with **low contrast** were harder to segment accurately.

| Failure Case | Example |
|-------------|---------|
| **Dark Background Issue** | ![Fail 1](assets/img/fail_dark.png) |
| **Tilted Window Issue** | ![Fail 2](assets/img/fail_tilt.png) |

