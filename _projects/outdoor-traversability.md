---
layout: page
title: "Autonomous Navigation Pipeline for Unstructured Outdoor Terrain"
description: "An outdoor navigation stack that started from analysis and verification of 3D Mesh Navigation and evolved into an in-house four-stage pipeline: FAST-LIVO2 mapping and localization → grid_map terrain assessment → global planning → Pure Pursuit-LOS tracking"
img: assets/img/projects/outdoor-traversability-autonomous/img-3.jpg
importance: 7
category: company
---

**Period** 2025.12 – present · **Affiliation** KETI (multi-robot cooperative navigation project, 4th-year outdoor extension · SDR) · **Role** Mesh Navigation analysis and simulation verification, Open3D point cloud correction, FAST-LIVO2 stack configuration, grid_map traversability and costmap implementation, path tracking controller design

## Background and Problem

A 2D Occupancy Grid is effective for flat indoor spaces, but it cannot represent slopes, steps, or bumps, so traversability information is lost. To navigate safely on unstructured outdoor terrain, the terrain must be represented in 3D, with localization, path planning, and tracking chained on top of it.

## Approach A — 3D Mesh Navigation (2025.12 – 2026.02, analysis and verification completed)

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/outdoor-traversability-autonomous/img-1.jpg" title="Mesh Navigation" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">RViz2 Mesh Map visualization / Localization registration / Gazebo simulation environment.</div>

- **Four-stage pipeline analysis:** Mapping (SLAM point cloud → Marching Cubes → Triangle Mesh) → Localization (MICP-L: scan-to-mesh ICP) → Planning (CVP: continuous vector field based on vertex Cost Layers) → Controller (Move Base Flex plugin). Verified end-to-end integration in Gazebo + RViz2.
- **Point cloud correction:** A horizontally mounted 3D LiDAR is parallel to the ground plane, so ground data is sparse and holes appear in the mesh. Open3D Z-filtering, ROI, and SOR preprocessing → three-point plane equation → grid point generation within the plane boundary (2.5 mm) → boundary duplicate handling → voxel downsampling and normal estimation → .ply mesh output via slam_to_mesh.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/outdoor-traversability-autonomous/img-2.jpg" title="Before correction" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/outdoor-traversability-autonomous/img-3.jpg" title="After correction" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Mesh from the point cloud before correction (left) and after correction (right) — ground density becomes uniform and holes almost disappear.</div>

## Approach B — In-House Four-Stage Pipeline (first half of 2026 – , current main track)

| Stage | Details |
| --- | --- |
| (1) Mapping and localization | **FAST-LIVO2** (LiDAR-IMU-Visual, sequential ESIKF) architecture analysis → ROS 2 Humble stack configuration (fork lineage, two-stage build) → colored point cloud map produced with in-house sensors, localization with fast_lio_localization |
| (2) Terrain assessment | **grid_map**-based four-formula pipeline (slope, roughness, step, traversable), FilterChain cross-validation, real-time TF |
| (3) Cost field and planning | Self-clearing Gaussian cost field, lightweight reimplementation of probabilistic elevation mapping (160k cells at 30 Hz, no GPU required), global map saving and local costmap |
| (4) Tracking | In-house hybrid_controller — $$\omega = \omega_{pp} + \omega_{los}$$ (Pure Pursuit + LOS) fusion, 6-state machine, quantitative verification by hand calculation (derived structural constraints such as a minimum turning radius of 0.67 m) |

Mesh (continuous surface, CVP vector field) has high expressiveness but a heavy stack, while 2.5D grid_map is lightweight and real-time, so Approach B is currently the main track. The platform is a Clearpath Jackal (LiDAR/IMU mast, NUC, micro-ROS drive).

## Results

- Simulation integration of the full Mapping → Localization → Planning → Controller pipeline, and resolution of real-environment data gaps through point cloud correction.
- Approach B pipeline configured and indoor demonstration completed; outdoor testing in progress.

## Tech Stack

FAST-LIVO2 · fast_lio_localization · grid_map / elevation mapping · Pure Pursuit · LOS Guidance · Mesh Navigation (MICP-L, CVP, MBF) · Open3D · slam_to_mesh · ROS 2 Humble · Gazebo / RViz2 · Livox Mid-360 · Clearpath Jackal · C++ / Python
