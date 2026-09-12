---
layout: page
title: "Quadruped Wheel-Leg Hybrid Robot (Mechathon · Capstone)"
description: "A hybrid robot that normally drives on wheels and, at obstacles the wheels cannot clear, controls its leg joints to switch into walking mode and step over them. Responsible for mechanical design, 3D modeling, and ROS-based integrated control"
img: assets/img/projects/wheel-leg-robot/img-3.png
importance: 2
category: personal
---

**Period** 2021.12 – 2022.06 · **Affiliation** Department of Robotics Engineering, Keimyung University (Mechathon; Capstone Excellence Award) · **Role** Idea proposal, hardware design and 3D modeling, ROS-based driving mode control

## Background and Goals

Wheeled robots are fast and efficient on flat ground but weak against steps, while legged robots adapt well to terrain but are slow and complex to control. The goal was to mount both locomotion mechanisms on a single platform and actively control the leg joints to enable body lifting and posture control as well.

## Approach

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/wheel-leg-robot/img-1.png" title="D-H parameters" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/wheel-leg-robot/img-2.png" title="Hardware design drawing" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/wheel-leg-robot/img-3.png" title="3D model" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">D-H parameter analysis of the leg joints (left), overall hardware design drawing (center), and the robot's 3D model (right).</div>

1. **Kinematic analysis:** Modeled the leg joints with D-H parameters (α, a, d, θ), computed the position of the wheeled leg tip with forward kinematics, and solved for the joint angles at a target position with inverse kinematics.
2. **Walking/driving mode switching:** Detect an obstacle with sensors → decide to enter walking mode → lift the legs with the actuators → move to the target position → return to driving mode after passing.
3. **ROS-based integrated control:** Modularized sensor data collection, decision making, and actuator control into nodes communicating asynchronously over topics, so that adding a sensor or algorithm only requires swapping the corresponding node.
4. **Hardware design and 3D modeling:** Designed a vertically adjustable body frame and the wheel-coupling structure of each leg in CATIA, verified interference, and fabricated the parts by 3D printing. Selected motors based on torque and sized the battery from current capacity calculations.

## Results

Implemented an autonomous motion sequence in ROS running "obstacle detection → actuator operation → robot lifting → avoidance driving," completing a hybrid platform that drives while actively changing its posture in response to the environment. The electrical and mechanical experience gained here later became the basis for electrical troubleshooting on real robots.

## Tech Stack

ROS (nodes/topics) · D-H parameters · Forward / Inverse Kinematics · CATIA · 3D printing · Sensor and actuator control · Motor and battery selection
