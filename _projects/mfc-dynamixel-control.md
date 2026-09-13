---
layout: page
title: "MFC Dynamixel Actuator Control"
description: "C++ MFC GUI driving position, velocity and current modes of an XM430 over TTL with real-time state monitoring."
img: assets/img/projects/mfc-actuator-control/img-1.png
importance: 5
category: personal
---

**Period** 2021.08 – 2021.12 · **Affiliation** Keimyung University (undergraduate researcher) · **Role** MFC GUI design and development, three-mode control implementation, bidirectional TTL communication, real-time monitoring

## Background and Goals

The goal was to control the actuator, the core drive component of a robot, directly and beyond the level of SDK examples. I programmed all three operating modes of the Dynamixel and configured the system to respond to GUI input in real time while monitoring the current state (position, velocity, current).

## Approach

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/mfc-actuator-control/img-1.png" title="MFC window" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mfc-actuator-control/img-2.png" title="MFC development process" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">The MFC window and its development process.</div>

1. **MFC GUI design:** A Windows application composed of Connection/Disconnection, Enable (torque on), Present Value (current state), Goal Value (target input), Torque Value, and MOVE buttons.
2. **Three control modes:** Position control (target angle, for joints), velocity control (continuous rotation at a target RPM, for wheels), and current control (precise torque control using the relationship $$\tau = K_t I$$, with the torque value displayed in real time).
3. **TTL communication:** PC USB → USB-to-TTL converter → Dynamixel. Multiple actuators daisy-chained over half-duplex TTL serial.
4. **Real-time monitoring:** Read the Present Value and compared it with the Goal Value to check control accuracy at a glance.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mfc-actuator-control/img-3.png" title="TTL communication" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mfc-actuator-control/img-4.png" title="XM430-W350" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">The TTL communication scheme (left) and the Dynamixel XM430-W350 used (right).</div>

## Results

Gained system-level programming experience in which C++/MFC communicates with and controls hardware directly, and learned the differences between position, velocity, and current control through real hardware responses. This experience later became the foundation for the integrated actuator and sensor control of the [wheel-leg robot](/projects/wheel-leg-robot/).

## Tech Stack

C++ · MFC · Dynamixel SDK · TTL half-duplex serial communication · Visual Studio · ROBOTIS XM430-W350
