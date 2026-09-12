---
layout: page
title: "Go2 Multi-Floor Building Simulation Testbed and VLM Autonomous Mission Agent"
description: "A testbed that ported a three-story building world with stairs, ramps, and a working elevator across Gazebo Harmonic → Isaac Sim 5.0 → MuJoCo to verify quadruped locomotion control, real-spec sensors, and a VLM orchestration agent"
importance: 8
category: company
---

**Period** 2026.06 – present · **Affiliation** KETI (SDR quadruped extension track · linked with the multi-robot cooperative navigation project) · **Role** World construction and porting pipeline, locomotion control and policy porting, sensor emulation, VLM agent experiments

## Background and Goals

A testbed was needed to verify, before deployment on the real robot, a scenario in which the quadruped robot **Unitree Go2** moves through a multi-floor indoor building with stairs, ramps, and a working elevator. The goals were (1) a shared multi-floor building world, (2) a system for porting locomotion controllers and RL policies across simulators, (3) perception verification with real-spec sensors, and (4) an experimental platform for higher-level autonomy (VLM agent).

## Three Simulator Tracks

| Item | Gazebo Harmonic | Isaac Sim 5.0 / IsaacLab | MuJoCo |
| --- | --- | --- | --- |
| GPU | Not required | RTX required | Not required |
| World source | **Canonical SDF** — procedural three-story building generator | SDF → USD (metadata contract) | SDF → MJCF idempotent conversion script |
| Elevator | Working plugin | GPU-resident FSM | FSM + visitor and pedestrian emulation |
| Locomotion control | Quantitative comparison of 4 controllers: in-house trot/crawl, OCS2, CHAMP | IsaacLab RL training and execution | 2 frozen policies (PGTT flat, IsaacLab uneven) + adapter, height-scan-based automatic terrain switching and stair assist |
| Sensors | Standard plugins | Physics-perception consistency via terrain baking, standard ROS 2 topics (Jazzy) | **Real-spec emulation** — Mid-360 non-repetitive scan and intensity, D455 depth degradation, etc. |
| Best suited for | World construction, low-cost integration verification | RL training, high-fidelity perception | Lightweight iterative experiments, policy porting, agent testbed |

## Key Outcomes

- **Shared world porting contract:** Established a pipeline that converts a once-built SDF building into USD and MJCF, reusing it across three physics engines.
- **Policy porting system:** Separated training (IsaacLab / MJX) from execution (MuJoCo), and completed a ground-floor-to-second-floor stair climb with frozen policies + adapter + terrain-based automatic switching (height-scan z-range, asymmetric immediate/3 s). Verified with a self-contained package of 90 tests.
- **VLM orchestration autonomous mission agent:** Following the principle "the VLM decides, the sensors provide the facts," a local 27B VLM (single GPU) was layered on a deterministic tool layer; with only a natural-language mission and no prior map, it discovered and boarded the elevator and reached the second floor (full success in 7 turns).
- In parallel, analyzed the SDK structure of the bipedal platform LimX TRON1 and verified control through a Low-level API shared between simulation and the real robot.

## Related Reviews

The flat policy on the MuJoCo track is a port of the public policy from the [PGTT](/blog/2026/pgtt/) paper, and the higher-level autonomy experiments are closely related to the legged VLN line of research such as [NaVILA](/blog/2026/navila/).

## Tech Stack

Unitree Go2 · ROS 2 Humble / Jazzy · Gazebo Harmonic · Isaac Sim 5.0 / IsaacLab · MuJoCo / MJX · SDF / USD / MJCF · OCS2 · CHAMP · RL policy adapter · Local VLM · Python / C++
