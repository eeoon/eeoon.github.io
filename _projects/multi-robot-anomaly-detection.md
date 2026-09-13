---
layout: page
title: "Multi-Robot Anomaly Detection"
description: "Fault injection on a 30-robot fleet, rule-based detection with a web dashboard, then Transformer self-supervised detection (Aff-F1 0.94)."
img: assets/img/publication_preview/anomaly-detection-visualization-rmf.png
importance: 4
category: company
related_publications: true
---

**Period** 2025.03 – 2026.05 · **Affiliation** KETI (multi-robot cooperative navigation national R&D project) · **Role** Fault injection and logging system development, anomaly detection framework design, model training and evaluation, web visualization

## Background and Problem

In environments dense with multiple robots, a small fault in an individual robot (sudden battery drop, localization failure, navigation control failure) can propagate into fleet-wide bottlenecks and collisions. However, existing RMF focuses on visualizing individual robot states and lacked the means to diagnose inter-robot dependencies or cooperation failures, and threshold-based detection struggled to capture the irregularity of real anomaly data.

## Approach

### Phase 1 — Fault Injection and Data Collection

- Built a **PySide6 Fault Injection GUI** that injects and recovers hardware (battery, wheel), Localization Lost, obstacle, and sensor (LiDAR, camera) faults on simulated robots.
- Built a system that logs multi-robot information into a unified ROS 2 CSV (30 robots, 10 Hz downsampled to 1 Hz), later used as training data.

### Phase 2 — Rule-Based Anomaly Detection and Web Visualization (ICCAS 2025) {% cite kim2025anomaly %}

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/publication_preview/anomaly-detection-visualization-rmf.png" title="Anomaly framework" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Anomaly Detection Framework — RMF (Task Allocation) → Anomaly State (4 types) → Web Dashboard → Operator.</div>

- Defined four anomaly types at the **individual robot level** (Battery Depletion, Obstacle Detection) and the **fleet level** (Trajectory Conflict, Collision Risk — paths crossing within 0.2 m of another robot).
- In a modular structure where RMF handles task allocation and a separate sensor/log-based module handles anomaly detection, a WebSocket/ROS Bridge-based web dashboard delivered real-time alerts and visualization to the operator. Verified in a 30-robot Gazebo environment.

### Phase 3 — Learning-Based Anomaly Detection (ICROS 2026) {% cite kim2026learning %}

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/publication_preview/learning-anomaly-detection-framework.png" title="10-robot simulation" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Open-RMF-based 10-robot simulation environment (Gazebo + RMF visualization).</div>

- Normalized each robot's time series into a 4-dimensional feature $$X_t = [P_t, V_t, B_t, R_t]$$ (position error, velocity, battery, mission progress).
- After LSTM time-series prediction and Transformer AutoEncoder reconstruction-based detection, adopted **RESTAD**, a self-supervised model that embeds an RBF layer in a Transformer, to learn spatio-temporal correlations among multiple robots without labels.
- Applied it to real robots through a real-time ROS 2 streaming pipeline (based on Motion Residual).

## Results

| Phase | Outcome |
| --- | --- |
| Rule-based (ICCAS 2025) | Real-time detection and visualization of four anomaly types, verified in a 30-robot simulation, first author, IEEE Xplore |
| Learning-based (ICROS 2026) | Region-level **Aff-F1 0.94** — outperforming LSTM and a plain Transformer, first author |
| Operational visibility | PySide6 integrated fleet management GUI + Fault Injection GUI + web dashboard |

## Tech Stack

Open-RMF · ROS 2 · Gazebo · PySide6 / rclpy · WebSocket / ROS Bridge · PyTorch (LSTM, Transformer AE, RESTAD) · CSV logging
