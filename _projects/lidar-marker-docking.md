---
layout: page
title: "Precision Charging Docking Based on LiDAR Marker ICP Registration"
description: "A two-stage docking system (Nav2 approach + fine alignment) that evolved through three phases — ArUco vision → 2D LiDAR trapezoidal geometric marker → ICP registration — to achieve ±3–5 cm precision docking in a real environment"
img: assets/img/projects/lidar-marker-charging/img-6.png
importance: 5
category: company
---

**Period** 2025.10 – 2025.12 · **Affiliation** KETI (SDR national R&D project) · **Role** Marker recognition pipeline implementation, LiDAR geometric recognition algorithm design, ICP registration and parameter tuning, Nav2 docking integration, TETRA_S real-robot validation

## Background and Problem

Conventional LiDAR autonomous navigation is effective for approaching the vicinity of a destination, but it falls short for **centimeter-level precision alignment** such as charging docking, designated work positions, or stopping in confined spaces. The goal was to build a docking system that works in a real environment by progressively compensating for the strengths and weaknesses of each recognition method.

## Approach

### Phase 1 — ArUco Vision Docking (Simulation)

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/lidar-marker-charging/img-1.png" title="ArUco parking" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Gazebo parking environment with an attached ArUco marker and the marker recognition result.</div>

Binarization → contour/quadrilateral candidates → Homography perspective correction → ID decoding → 6-DOF pose via PnP → TF transform (camera → base_link → odom → map). After approaching with Nav2, the robot stops precisely with the marker coordinates as the target. The limitation was sensitivity to lighting, noise, and occlusion.

### Phase 2 — 2D LiDAR Geometric Marker (Simulation)

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/lidar-marker-charging/img-2.jpg" title="Trapezoidal marker" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Simulation environment with trapezoidal markers of various sizes.</div>

Without a camera, an asymmetric trapezoidal marker (60° at the bottom / 120° at the top) was identified through LiDAR line segmentation and the ratios of angles and edge lengths. Scale invariance was verified — the marker is recognized regardless of size as long as the angular structure is preserved — and larger markers yielded higher point density and therefore higher accuracy.

### Phase 3 — ICP Registration Docking (Real-Environment Demonstration)

A user-defined trapezoidal template $$T$$ and the real-time scan $$S$$ are registered with Point-to-Point ICP. KD-Tree nearest-neighbor correspondence ($$O(\log M)$$) → optimal $$R = VU^{T}$$, $$t = \bar{t} - R\bar{s}$$ via SVD of the centroids and cross-covariance $$H$$ → convergence check (Δt, Δθ, MSE) → success/failure decision by fitness score. ICP is activated only in the low-speed segment after the Nav2 approach, real-time performance was secured through KD-Tree reuse and downsampling, and the docking parameters (retries, abort_distance, docked_distance_threshold, etc.) were finalized through repeated experiments.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/lidar-marker-charging/img-3.png" title="Before registration" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/lidar-marker-charging/img-4.png" title="After registration" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Before (left) and after (right) ICP registration — white template and green LiDAR scan.</div>

<div class="row mt-3">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/lidar-marker-charging/img-6.png" title="Real docking" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">The actual docking frame and the TETRA_S robot in operation.</div>

## Results

- Integrated two-stage control — Nav2 global approach → ICP fine alignment — and demonstrated **±3–5 cm** precision docking in a real environment.
- Combined with simulation-based battery charging logic to complete an automatic charging scenario, and later verified the Docking function on top of the OTA framework without code modification.

## Tech Stack

OpenCV ArUco · PnP · Line Segmentation · ICP · KD-Tree · SVD · Nav2 Docking Server · TF · ROS 2 Humble · Gazebo · PCL · Jackal / TETRA_S · C++ / Python
