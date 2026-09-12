---
layout: page
title: "SDR — Docker and Git-Based OTA Deployment Framework"
description: "An OTA system for Software-Defined Robots that modularizes robot functions into containers and separates shared code from Robot Profiles to deploy and verify across multiple robots without downtime"
img: assets/img/publication_preview/ota-update-framework-ros2-docker.png
importance: 3
category: company
related_publications: true
---

**Period** 2025.01 – present · **Affiliation** KETI (national R&D project on SDR domain service business model development and robot implementation/verification) · **Role** OTA deployment framework design and implementation, Robot Profile structure design, per-function verification

## Background and Problem

As the number of field robots grows and their functions change, modifying and reinstalling code on each robot makes maintenance impossible. To realize the **SDR (Software-Defined Robot)** paradigm — extending functionality through software updates after release, as with smartphones or SDVs (Software-Defined Vehicles) — a system was needed that reduces hardware dependence and updates software remotely.

## Approach

<div class="row justify-content-sm-center">
    <div class="col-sm-9 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/publication_preview/ota-update-framework-ros2-docker.png" title="SDR OTA architecture" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Overall SDR OTA architecture — dual Public Cloud + Isolated Network structure, Dashboard, Simulator, Marketplace, State/Usage Logger, Docker Registry, Git Repository (ICCAS 2025).</div>

- **Container modularization:** Split robot functions (Navigation, Patrol, Elevator, Docking) into container units based on Docker and Git, and built OTA scripts that automate backup → stop → clone → restart. The robot PC holds only a Host OS and Docker Engine, and ROS 2 containers are deployed sequentially.
- **Separation of shared code and Robot Profile:** So that robots deployed from the same image can still have different navigation, boarding, and docking behaviors, per-robot differences were separated into a Robot Profile (YAML) on an external volume. The key point is decoupling the lifecycles of code and parameters.
- **Dual server:** Designed a structure supporting both a public cloud OTA server and an On-Premise server for isolated networks, and added a State Logger for debugging and a Usage Logger for billing and subscription management.
- **Phased verification:** After OTA deployment, the Navigation and Patrol functions (2026.02) and the Elevator and Docking functions (2026.03) were verified without code modification.

## Results

- Verified on a real robot (TETRA_S) a **zero-downtime deployment structure** that swaps and manages service modules (patrol, docking, elevator) without source modification.
- Completed a PoC with two shell scripts for initial installation and updates, and presented it at ICCAS 2025 (second author) {% cite kim2025ota %}.
- The [elevator integration](/projects/robot-elevator-interface/) and [LiDAR marker docking](/projects/lidar-marker-docking/) functions operate on top of this deployment system.

## Tech Stack

Docker · Git / GitHub · ROS 2 Humble · Robot Profile (YAML, external volume) · Bash · DDS · TETRA_S / Jackal
