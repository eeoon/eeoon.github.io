---
layout: page
title: "AGV–Manipulator Integrated TCP/IP Communication Protocol (Manufacturing Logistics)"
description: "Design of a custom TCP/IP protocol linking the supervisory controller (MasterPC), the ACS server, and stacker robots — STX/CR/LF framing, command ID matching, ACK/NACK with Reason codes, and HeartBeat"
img: assets/img/projects/agv-robotarm-tcpip/img-1.png
importance: 6
category: company
---

**Period** 2024.05 – 2024.09 · **Affiliation** KETI (Robot Plus competitiveness support program based on collaborative intelligence) · **Role** Communication protocol design, TCP/IP communication module and ACS communication manager implementation, packet structure definition and documentation

## Background and Problem

In manufacturing logistics automation, work command transmission and status monitoring between the supervisory control system (MasterPC) and field robots (stacker AGVs) must proceed without interruption. A communication specification was needed to connect the different control systems, and the goal was to secure command consistency and reliability as a bidirectional communication hub rather than a simple relay.

## Approach

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/agv-robotarm-tcpip/img-1.png" title="Overall communication architecture" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/agv-robotarm-tcpip/img-2.png" title="Control Master communication architecture" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Overall communication architecture (left) and Control Master communication architecture (right).</div>

- **Architecture:** `MasterPC ↔ TCP/IP ↔ ACS Server ↔ TCP/IP ↔ Stacker`. A dual structure in which the ACS Manager simultaneously acts as a Server (receives Master requests → forwards to ACS) and a Client (ACS responses → forwards to Master).
- **Packet framing:** Every packet starts with STX (0x02) and ends with CR/LF, with a CMD field and a data field in between. The letter case of CMD distinguishes the transmission direction (work command o/O, Pause/Resume p/P, status report s/S, HeartBeat h/H).
- **Main functions:** (1) Pick/Place work commands (priority 1–9, Command ID) (2) ACK/NACK responses with 9 Reason codes that diagnose the cause of failure (3) Pause/Resume (4) 7-stage work status reporting from ASSIGN to ORDER COMPLETE (5) per-AGV HeartBeat.
- **Design features:** Time-based Command IDs match responses in an asynchronous environment, and the cause of failure can be diagnosed beyond simple success/failure.

## Results

Built a stable communication infrastructure between the supervisory controller and field robots with a specification covering the entire communication lifecycle, from work commands to status monitoring and connection checks. It became the communication foundation for integrating heterogeneous logistics robots, including stacker AMR operation (MCP, VehiclePlanner, ACS, QR precision docking) and MiR 600 programming.

## Tech Stack

TCP/IP · Client/Server hybrid · STX/CR/LF packet framing · ACK/NACK protocol · C++ / C · Stacker AMR · ACS
