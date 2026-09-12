---
layout: page
title: "Open-RMF Integration of Heterogeneous Logistics Robots (Cooperative Intelligence)"
description: "Unified heterogeneous mobile robots (TETRA carts, stacker AMRs, MiR 600) under the Open-RMF multi-robot operating system, building up free_fleet modifications, docking control handover, sensor-based perception, and a PySide6 GUI in stages"
img: assets/img/publication_preview/openrmf-cooperative-autonomous.png
importance: 9
category: company
related_publications: true
---

**Period** 2023.05 – 2025.05 · **Affiliation** Korea Electronics Technology Institute (KETI), Cooperative-Intelligence Robot Plus Competitiveness Support Program · **Role** Multi-robot operating system setup, free_fleet modification and coordinate registration, docking control handover, sensor-based obstacle perception, external system communication integration, simulation and GUI tool development

## Progress by Phase

1. **Open-RMF and free_fleet core (2023):** Built nav_graphs with traffic_editor, added a custom robot model plugin (slotcar), wrote a verification checklist for multi-robot traffic, tasks, and visualization, analyzed free_fleet and registered the traffic_editor and Nav2 map coordinate frames, integrated the real TETRA_DSV_M robot (Cartographer mapping + ros1_bridge + free_fleet), and modified the free_fleet ROS 1 ClientNode with pause and arrival topics.
2. **Docking, obstacle avoidance, and control handover (H2 2023):** TETRA cart docking scenario, integration of rmf_obstacle_detectors and lane_blocker, design of a state machine (Moro_controller) that hands Open-RMF control over during docking and returns it afterward, and ROS 2 joy teleop.
3. **Mapping, sensor perception, and real-robot validation (2024):** Namespace-based Cartographer, PCD to PGM conversion (3D to 2D maps), LiDAR dynamic obstacle detection, camera-based YOLO object detection with 3D coordinate transformation, and a real-robot demonstration of YOLOv8 obstacle recognition with a RealSense D435.
4. **External system communication (H2 2024):** Stacker AMR operation (MCP, VehiclePlanner, ACS, QR-based precision docking), the stacker-to-ACS [TCP/IP communication protocol](/projects/agv-manipulator-tcpip/), and MiR 600 usage and programming.
5. **Simulation, networking, and GUI upgrades (2024–2025):** Battery system and LiDAR obstacle-avoidance simulation, review of Isaac Sim examples and their integration with Open-RMF, MQTT networking fundamentals (Mosquitto), migration from Gazebo Classic to Gazebo Sim, RViz2 URDF and TF visualization, and an integrated GUI built with Qt Designer, PySide6, and rclpy.

## Results

- Open-RMF integration of the real TETRA_DSV_M robot and a real-robot demonstration of YOLOv8 obstacle recognition.
- The project's themes of Open-RMF cooperative autonomous driving and cross-utilization of obstacle information led to the KROC 2024 {% cite kim2024openrmf %} and ICROS 2024 {% cite kim2024crossutilization %} papers, and the PySide6 fleet management GUI later became the foundation of the [anomaly detection fault injection GUI](/projects/multi-robot-anomaly-detection/).

## Tech Stack

Open-RMF · free_fleet · Nav2 · ROS 2 / ROS 1 (ros1_bridge) · Cartographer · Gazebo (Classic / Sim) · Isaac Sim · YOLOv8 · RealSense D435/D455 · TETRA cart · Stacker AMR · MiR 600 · PySide6 / Qt · MQTT
