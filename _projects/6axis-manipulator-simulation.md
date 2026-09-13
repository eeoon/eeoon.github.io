---
layout: page
title: "6-Axis Manipulator Kinematics"
description: "D-H parameters, forward/inverse kinematics and cubic-spline paths in MATLAB, verified against hand calculation."
img: assets/img/projects/6axis-robot-simulation/img-5.png
importance: 4
category: personal
---

**Period** 2021.09 – 2021.11 · **Affiliation** Keimyung University (personal project) · **Role** Carried out everything alone, from kinematic modeling to MATLAB simulation implementation

## Background and Goals

An industrial collaborative robot can only place its end-effector at the intended position if the relationships between links are modeled mathematically and precisely. The goal was to understand kinematics end to end by deriving and implementing the D-H parameters myself, and to verify that hand-calculated values agreed with the simulation results.

## Approach

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/6axis-robot-simulation/img-1.png" title="D-H parameters" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/6axis-robot-simulation/img-2.png" title="Transformation matrices" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Coordinate frame assignment and D-H parameter definition (left), and derivation of the transformation matrices (right).</div>

1. **Coordinate frame assignment and D-H derivation:** Assigned the origin of each joint, set the Z axis along the rotation axis and the X and Y axes by the right-hand rule, and defined the four parameters (d, θ, a, α).
2. **Transformation matrix derivation:** Generalized the joint-to-joint transformation matrices with D-H and derived the base-to-end-effector relationship as $$T_0^n = A_0^1 A_1^2 \cdots A_{n-1}^n$$.
3. **MATLAB implementation:** Implemented the transformation matrix function, link/joint plotting, the initial pose, and cumulative visualization of user input in a structure separated into function files and a run script.
4. **Path planning:** To compensate for the instability of straight-line paths (stop → constant velocity → stop is not achievable), applied a cubic spline $$\theta(t) = A\left(1 - \cos\frac{n\pi}{T}t\right)$$ with zero angular velocity at departure and arrival.
5. **Inverse kinematics:** Solved for each θ from the end-effector position. Used atan2 to ensure continuity over −π to π, and classified cases where the denominator becomes zero in the a = 0 transformation matrix as singularities.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/6axis-robot-simulation/img-3.png" title="MATLAB implementation" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/6axis-robot-simulation/img-4.png" title="Cubic Spline" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/6axis-robot-simulation/img-5.png" title="Verification" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">MATLAB simulation (left), cubic spline path planning (center), and verification that hand calculations match the simulation (right).</div>

## Results

- With inputs θ₂ = 60° and θ₅ = 60°, the hand-calculated (X, Y, Z) = (117, 1170, 1127) matched the simulation result exactly, verifying the accuracy of the model.
- Completed an interactive tool in which the user enters θ values and observes the pose change in real time.
- Remaining work: automatic singularity detection and avoidance, Jacobian-based velocity control, and Simulink integration.

## Tech Stack

MATLAB · D-H Parameters · Forward / Inverse Kinematics · Cubic Spline · Homogeneous transformation matrices
