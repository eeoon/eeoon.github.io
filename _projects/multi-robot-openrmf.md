---
layout: page
title: "Open-RMF Multi-Robot Fleet Management"
description: "Heterogeneous fleet under one supervisor: 30-robot simulation to 4 real robots, cooperative obstacle sharing and re-planning."
img: assets/img/publication_preview/openrmf-cooperative-autonomous.png
importance: 1
category: company
related_publications: true
---

**Period** 2023.05 – present · **Affiliation** Intelligent Robotics Research Center, Korea Electronics Technology Institute (KETI) · **Role** Design and real-robot validation of the multi-robot simulation and fleet management system

## Background and Problem

For dozens of mobile robots with different control systems to operate together in a large indoor workspace, a supervisory architecture is needed that assigns missions centrally and runs the fleet without collisions. When many robots run simultaneously, global path interference and server communication bottlenecks were the core challenges. This project covers the full process of unifying heterogeneous robots under one fleet manager based on [Open-RMF](https://www.open-rmf.org/), and transferring the logic verified in simulation to physical robots.

## Responsibilities

- **Multi-robot simulation infrastructure:** Extended Gazebo models from TurtleBot3 to TETRA_S/TETRA_M with namespace and TF design, Nav2 multi-robot configuration, ROS 1 to ROS 2 (Foxy/Humble) migration, and sensor driver cleanup (LiDAR, IMU, RealSense).
- **Open-RMF fleet management and task system:** Built traffic_editor nav_graphs, integrated fleet_adapter and free_fleet, implemented user-input-driven go_to_place dispatch, a node that automatically assigns endlessly repeating missions (RMF Infinity Task), and a battery-plugin-based charging task.
- **Cooperative perception and navigation:** Each robot converts obstacles detected with a 2D LiDAR and RGB-D camera (YOLOv8) into global map coordinates and shares them with the server; lanes intersecting an obstacle are closed (Lane Closed) so that other robots proactively detour around obstacles outside their own field of view.
- **Communication optimization:** Resolved communication bottlenecks and data loss in real-robot operation through CycloneDDS configuration, and extended connections to robots on other domains with a Zenoh bridge. Demonstrated image_transport-based fleet management in a WiFi-6E environment.
- **Path planning analysis:** Analyzed the structure of centralized (CBS) and decentralized (VO, RVO, ORCA) MAPF algorithms and verified them in Gazebo.
- **Handover and integration testing:** State-based Replan Trigger ROS 2 service, ROS 2 emergency-stop topic, server-centric TF restructuring, yearly integration tests, and definition of performance metrics and data collection for certified testing.

## Results

- Verified the fleet management logic in a **30-robot** multi-robot simulation (RMF Gazebo World conversion, LiDAR avoidance, YOLO obstacle sharing), then completed a Sim-to-Real demonstration in which **4 physical robots** executed missions without deadlock.
- Confirmed through 4-robot experiments that cooperative obstacle information sharing reduces collision risk and path cost compared with a non-cooperative system (ICCAS 2024).
- Related papers: KROC 2024 {% cite kim2024openrmf %}, ICROS 2024 {% cite kim2024crossutilization %}, ICCAS 2024 {% cite kim2024traversability %} — first author on all three.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/publication_preview/openrmf-cooperative-autonomous.png" title="Open-RMF architecture" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Full Control Fleet Adapter ↔ ROS 2 Free Fleet Server ↔ Free Fleet Client (ROS 1/ROS 2 mobile robots) architecture (KROC 2024).
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/publication_preview/cooperative-path-planning-obstacle-info.png" title="Obstacle detection" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/publication_preview/traversability-assessment-path-planning.png" title="Multi-robot framework" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Left: 2D LiDAR sampling + RGB-D YOLOv8 obstacle detection (ICROS 2024). Right: Robot (State/Navigation/Object Detection) ↔ RMF (Task/Traffic/Schedule) framework (ICCAS 2024).
</div>

## Tech Stack

Open-RMF · ROS 2 (Foxy/Humble) · Nav2 · Cartographer · Gazebo · CycloneDDS / Zenoh · YOLOv8 · TETRA_S / TETRA_M · C++ / Python
