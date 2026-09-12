---
layout: page
title: "Stability Assessment and PID Design for a 3-Axis 7-DOF Spring-Damper System"
description: "A MATLAB control project that extended a 1-axis 3-DOF vibration system to 3 axes and 7 DOF, assembled a 28×28 combined state space, and stabilized the uncontrolled, unstable system with PID"
img: assets/img/projects/3axis-7dof-stability/img-4.png
importance: 3
category: personal
---

**Period** 2021.02 – 2021.08 · **Affiliation** Keimyung University (undergraduate researcher, Robot Dynamics and Intelligent Control Lab) · **Role** Mathematical modeling, state-space design, MATLAB stability simulation, PID controller design

## Background and Goals

The ultimate goal was a self-stabilizing (disturbance rejection) system in which the robot body returns to equilibrium after an external force and is ready for the next one. Starting from a simple 1-axis model, I extended it progressively to a 3-axis system and quantified how the added complexity affects stability.

## Approach

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/3axis-7dof-stability/img-1.png" title="3-DOF modeling" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/3axis-7dof-stability/img-2.png" title="Step response" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Equations of motion and modeling of the 1-axis 3-DOF system (left) and the MATLAB step response (right).</div>

1. **1-axis 3-DOF modeling:** Derived the equations of motion for masses m₁, m₂, and m₃ connected in series by springs (k) and dampers (c).
2. **First stability check:** Tracked the position change of the central mass m₂ when a 455 N external force was applied to m₁. Confirmed that even without a controller the step response amplitude decayed and stability was maintained.
3. **Extension to 3 axes and 7 DOF:** Extended the structure so that six masses (m₁–m₆) are arranged radially around a central mass (m₇).
4. **State-space integration:** Converted the equations of motion of each axis to $$\dot{x} = Ax + Bu,\; y = Cx + Du$$ and combined the two 14×14 systems block-diagonally into a **28×28 combined matrix**, analyzing them simultaneously in a single environment.
5. **PID design:** Confirmed that after the 3-axis extension stability was no longer guaranteed without control, then designed a PID controller for each axis and derived the gains through simulation.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/3axis-7dof-stability/img-3.png" title="7-DOF concept diagram" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/3axis-7dof-stability/img-4.png" title="7-DOF modeling" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/3axis-7dof-stability/img-5.png" title="State variable matrix" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Concept diagram of the 3-axis 7-DOF system (left), modeling (center), and the state variable matrix of the state equation (right).</div>

## Results

Discovered through simulation that the stability present in the 1-axis case is no longer guaranteed without control once the system is extended to 3 axes and 7 DOF, and worked through the full engineering cycle of "problem identification → analysis → PID solution." Established a systematic methodology for determining gains via simulation.

## Tech Stack

MATLAB (Control System Toolbox) · Equations of motion / state-space representation · Spring-damper vibration systems · PID control · Linear algebra
