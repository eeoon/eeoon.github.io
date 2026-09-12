---
layout: page
title: "Multi-Floor Autonomous Navigation with Mobile Robot ↔ Elevator Integration"
description: "A system in which the robot calls, boards, and exits an elevator on its own via an MQTT–ROS 2 bridge and a custom NavigateMultiFloor Action. LiDAR scattering off metallic elevator surfaces was resolved with an SOR filter"
img: assets/img/projects/mobile-robot-elevator-interface/img-1.png
importance: 2
category: company
---

**Period** 2025.05 – 2025.09 · **Affiliation** KETI (SDR national R&D project) · **Role** MQTT–ROS 2 bridge design and implementation, multi-floor movement sequence control, sensor preprocessing, real-robot validation

## Background and Problem

Conventional robot autonomous navigation is centered on single-floor navigation. Elevator integration is essential for multi-floor building services, but there were four barriers. (1) The elevator communicates over MQTT while the robot uses ROS 2. (2) Experiments with a real elevator are heavily constrained in repeatability, safety, and cost. (3) The multi-stage sequence of call, board, floor transfer, and exit must be controlled as states. (4) LiDAR scattering off the metal and glass surfaces inside the elevator car breaks localization.

## Approach

<div class="row justify-content-sm-center">
    <div class="col-sm-9 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/mobile-robot-elevator-interface/img-1.png" title="System architecture" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Overall system architecture — a three-layer abstraction of ROS 2 robot / Bridge / MQTT elevator.</div>

1. **MQTT↔ROS 2 Bridge (Phase 1):** A bidirectional Bridge Node abstracts the robot, bridge, and elevator into three layers so that the robot side never handles MQTT directly. The API was defined by separating state into Topics (ArriveResponse, CallRequest, CmdResult) and commands into Services (CallElevator, ArriveElevator, RequestBoard/Unboard, UpdateBoardState).
2. **Gazebo verification (Phase 2):** Since the default Nav2 Action handles only a single floor, a custom **NavigateMultiFloor** Action was designed to control "check current floor → call → board → floor transfer → exit → destination coordinates" as a single integrated mission. Verified repeatedly in a multi-floor Gazebo world with a Clearpath Jackal and an elevator plugin.
3. **TETRA_S real-robot demonstration (Phase 3):** Connected the bridge to an elevator in actual service and executed the full process through a 9-state flow based on Nav2 (NavFn/DWB): IDLE → move to waiting point → call → wait for arrival → board → wait inside → exit → destination.
4. **SOR LiDAR preprocessing (Phase 4):** Statistical Outlier Removal compares the mean distance to each point's K nearest neighbors against the global distribution (μ, σ) and removes only isolated points exceeding μ+α·σ, preserving wall structure while selectively filtering spurious reflections.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-2.png" title="Multi-floor Gazebo environment" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-3.png" title="Gazebo experiment" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">The Jackal robot in the multi-floor Gazebo environment and a simulated elevator boarding scene.</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-4.png" title="Demonstration 1" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-5.png" title="Demonstration 2" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Real-building demonstration — TETRA_S calling / boarding / waiting inside / exiting.</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-7.png" title="Before SOR" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-8.png" title="After SOR" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">LiDAR scan comparison before (left) and after (right) applying the SOR filter — isolated reflection points are removed and the wall contours remain continuous.</div>

## Results

| Item | Result |
| --- | --- |
| MQTT↔ROS 2 conversion success rate | **100%** in both directions |
| Average conversion latency | **30–60 ms**, no packet loss under multiple concurrent calls |
| Real-robot demonstration | TETRA_S reliably completed the full call–board–wait–exit sequence on a real elevator, continued Nav2 navigation after exiting, with no path interruption |
| Sensor | After SOR, many isolated points were removed and obstacle perception matched the real environment |

The SOR troubleshooting in this project later carried over to the [SDR OTA project](/projects/sdr-ota-framework/), where the Elevator and Docking functions were verified on top of the OTA framework without code modification.

## Tech Stack

ROS 2 Humble · MQTT (Mosquitto) · In-house MQTT–ROS 2 Bridge · Nav2 (NavFn / DWB) · Custom Action · 2D LiDAR / IMU / Encoder · SOR · Gazebo + Elevator Plugin · Jackal / TETRA_S · C++ / Python
