---
layout: page
title: 3D Scene Reconstruction from Monocular Images using SFM
description: A classical computer vision pipeline for 3D scene reconstruction using monocular images.
img: assets/img/sfm img.jpg
importance: 2
category: Computer Vision and 3D Reconstruction
---

Github Link to the project - [Link](https://github.com/hitesh-vs/StructurefromMotion)

This project reconstructs a 3D scene and estimates camera poses using a given set of six monocular images and their feature point correspondences. The pipeline involves feature detection, camera pose estimation, and 3D point triangulation.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="images/flowchart.png" title="Pipeline Flowchart" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Pipeline Flowchart showing the overall process of SfM.
</div>

---

## Pipeline

1. **Feature Detection & Correspondences**  
   - Extract keypoints and match features across images.

2. **Fundamental & Essential Matrix Estimation**  
   - Compute the fundamental matrix to remove outliers and the essential matrix to estimate relative camera poses.

3. **Camera Pose Recovery**  
   - Solve for possible camera poses and determine the correct one.

4. **Linear Triangulation**  
   - Estimate 3D points from matched feature correspondences.

5. **Non-Linear Triangulation**  
   - Refine 3D points using optimization techniques.

6. **Pose Estimation (PnP & RANSAC)**  
   - Use Perspective-n-Point (PnP) with RANSAC to estimate camera poses.

7. **Bundle Adjustment**  
   - Optimize camera parameters and 3D points for better accuracy.

---

## Output

- **Sparse 3D Point Cloud**: A set of reconstructed 3D points representing the scene.
- **Camera Poses**: Estimated positions and orientations of the cameras.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="images/Final sfm.png" title="Pipeline Flowchart" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Final 3D reconstruction of the Unity Hall Building at WPI using SfM (Top View)
</div>

---

## References

- [SciPy Cookbook - Bundle Adjustment](https://scipy-cookbook.readthedocs.io/items/bundle_adjustment.html)
