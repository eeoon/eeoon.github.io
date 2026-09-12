---
layout: post
title: "Elevator-LIO: Robust LiDAR-Inertial Odometry for Multi-Floor Navigation under Elevator-Induced Non-Inertial Motion (arXiv 2026)"
date: 2026-06-11 09:00:00 +0900
description: A multi-floor LIO that separates the robot's relative motion from the cabin's transport motion in the state inside a moving elevator and suppresses vertical drift with a ZUPT at the stop instant
tags: [lio, elevator, multi-floor, navigation]
categories: paper-review
thumbnail:
giscus_comments: false
related_posts: false
toc:
  sidebar: left
---

| Item | Details |
|---|---|
| Authors | Yifan Zhang, Yudong Huang, Yuchong Zhang, Changze Li, Haoran Liu, Ming Yang, Tong Qin (Shanghai Jiao Tong University, SJTU) |
| Venue | arXiv preprint 2026 (arXiv:2605.24495, 2026-05-23; the formal conference/journal venue has not been confirmed, under double-blind review) |
| Links | [arXiv](https://arxiv.org/abs/2605.24495) · [Project page](https://xiaofan4122.github.io/Elevator_LIO_Page/) — code/dataset/simulator are to be released after publication and are currently not public |

## One-line Summary

When a robot rides a moving elevator, the elevator's transport motion mixes into the IMU acceleration, and existing LIO systems that mistake it for ego-motion diverge or drift severely. Elevator-LIO decouples the robot's relative motion and the elevator's transport motion in the state, and exploits the physical constraint at the instant the elevator stops as a ZUPT (zero-velocity/zero-acceleration correction) to suppress vertical drift. Because it is built minimally invasively on the FAST-LIO2 skeleton, it automatically reduces to a standard LIO in ordinary indoor settings.

## Lineage and Trends

- LIO mainstream: from LIOM, the line split into LIO-SAM (smoothing/factor-graph) and FAST-LIO/FAST-LIO2 (iterated ESKF + ikd-Tree), continuing to Point-LIO, DLIO, Faster-LIO, and VoxelMap. This paper inherits the IESKF + ikd-Tree skeleton of the FAST-LIO2 family as is, and augments it with elevator non-inertial states.
- Weak observability and moving-base estimation: Ground-Fusion/VIWO (wheel and vehicle kinematic constraints), ZUPT (zero-velocity detection in inertial navigation, Skog 2010), and contact constraints in legged robots (VILENS, Leg-KILO, LIKO). The idea of this family, that "intermittent physical constraints suppress drift under weak observability", is applied to the elevator stop event.
- Multi-floor and elevator-aware SLAM: MuNES, NV-LIO, and others focus on floor-transition management, mapping, and navigation. The authors' claim is that "no work has directly addressed continuous localization itself while the robot is inside a physically moving elevator", and this gap is the positioning of the paper.
- Scene-scale adaptation: LVIO-Fusion (multi-modal), AD-LIO (adaptive voxels based on scene degradation). This paper's adaptive downsampling is a lightweight variant of this family.

For reference, the official arXiv title is as written above, and in some sources the paper also circulates under the subtitle "A Robust LiDAR-Inertial Odometry in Non-Inertial Frames and Confined Spaces". It is the same paper.

## Problem and Motivation

Standard LIO assumes that "the sensor platform moves in an inertial frame, therefore IMU acceleration is the robot's ego-motion". When the robot enters a moving elevator cabin, this assumption collapses.

1. Estimation perspective: the measured acceleration superimposes the elevator's transport motion and the robot's motion relative to the cabin. Without separating them, integration error accumulates. Moreover, inside the closed cabin the LiDAR only observes motion relative to the cabin's interior geometry, so the absolute vertical-motion state becomes unobservable and its covariance keeps growing.
2. Front-end perspective: the scene scale changes abruptly between a wide floor and a confined cabin. With a fixed voxel resolution, points are excessively pruned in confined spaces, causing degeneracy or failure, while in wide spaces there are too many points and real-time performance drops.

Consequently, existing LIO mistakes the elevator motion for robot motion and produces severe vertical drift, map inconsistency, and divergence.

## Key Ideas and Method

### Non-inertial state decoupling

The state is split into the robot state relative to the elevator frame $$F_E$$, $$\mathbf{x}_{rel}$$, and the elevator transport state relative to the world, $$\mathbf{x}_{elev}$$. Starting from the generalized relative-motion theorem (including transport, Coriolis, Euler, and centripetal accelerations), it is reduced under elevator simplification assumptions. Ignoring cabin rotation ($$\boldsymbol{\omega}_E \approx 0$$) and setting the elevator frame parallel to the world ($$\mathbf{R}^W_E = \mathbf{I}$$), the transport motion reduces to pure vertical translation.

$$\mathbf{a}^W_I = [0,\ 0,\ a^W_{Ez}]^\top + \mathbf{a}^E_I$$

The final state is 18-dimensional: $$\mathbf{x}_{rel} = [\mathbf{p}^E_I, \mathbf{q}^E_I, \mathbf{v}^E_I, \mathbf{b}_a, \mathbf{b}_\omega]$$ and $$\mathbf{x}_{elev} = [p^W_{Ez}, v^W_{Ez}, a^W_{Ez}]$$ (the elevator's vertical displacement, velocity, and acceleration).

Intuitively, "how the robot moves inside a room" (relative) and "how that room itself is carried up and down" (transport) are recorded in separate ledgers. The reason only the elevator acceleration $$a^W_{Ez}$$ is kept as an explicit latent state is that transport acceleration varies relatively smoothly and is well modeled as a random walk, whereas the robot's relative acceleration fluctuates strongly and is better corrected indirectly through LiDAR updates. Higher-order terms such as jerk were excluded because they only increase dimension and noise. Gravity is fixed after static initialization: in the elevator frame, gravity is strongly coupled with the biases and the transport acceleration, so online estimation is numerically unstable.

### Mode-dependent IESKF

The same augmented state is always carried, but a mode manager decides in/out. In non-inertial mode, the elevator coupling block and the transport-state block are activated in the transition matrix $$\mathbf{F}_x$$ and the noise matrix $$\mathbf{F}_w$$. In inertial (normal) mode, those blocks are deactivated so that the estimator automatically degenerates to a standard FAST-LIO2-style LIO. It behaves like an ordinary LIO in normal indoor settings, and the extra terms switch on only in the elevator.

The LiDAR update is performed with point-to-plane residuals in the elevator-local frame $$F_E$$. The Jacobian of the residual with respect to the transport state $$\delta\mathbf{x}_E$$ is zero, which is the mathematical root of the fact that "the transport state is weakly observable in a closed cabin". The transport state is not frozen; it keeps propagating through the IMU process model, but only its covariance grows, so the exit correction is essential. The world odometry is composed by lifting the local relative position by the elevator height.

$$\mathbf{p}^W_I = \mathbf{p}^E_I + p^W_{Ez}\,\mathbf{e}_3$$

### Exit correction: Zero-State Update and Re-anchoring

The instant the elevator stops is physically a stationary state, so the transport velocity and acceleration must be zero. This is turned into a high-confidence prior observation, and a single EKF-style update (observation noise on the order of $$\mathbf{R} = \mathrm{diag}(10^{-5}, 10^{-4})$$) strongly pulls $$v^W_{Ez}$$ and $$a^W_{Ez}$$ to zero. The key is that, through the cross-covariance structure, this correction pulls down the accumulated vertical drift.

Re-anchoring follows. The elevator displacement is absorbed into the relative position ($$\mathbf{p}^E_I \leftarrow \mathbf{p}^E_I + p^W_{Ez}\,\mathbf{e}_3$$) and the transport state is reset to zero. The system returns to standard LIO coordinates while preserving the global position, and to prevent the uncertainty accumulated during the non-inertial phase from contaminating subsequent navigation, the cross-covariance blocks are set to zero and the diagonal is reset to a small prior.

As an analogy, inside the elevator "how many floors I have risen" floats uncertainly, and the moment the door opens and one's feet touch solid ground, one pins down "this is exactly this floor" and cleans up the floating error.

### Elevator Mode Manager: event triggers

Entry (Flag_Entry) uses the spatial-confinement pattern. When the 94th-percentile horizontal distance $$d_{max}$$ of valid LiDAR points stays below the threshold $$d_{th} = 3.0$$m for $$\Delta t_{door} = 2.0$$s, "door closed = entry" is declared. The percentile and duration conditions provide robustness to noise and dynamic occlusion.

Exit (Flag_Exit) is triggered when a stop is confirmed by tracking, with an FSM, the sliding-window variance pattern of the estimated vertical velocity $$v^W_{Ez}$$ (a constant-speed low-variance segment between the two peaks of acceleration and deceleration). No external infrastructure (building IoT) is needed; only onboard sensing is used. The interface is modular, exposing only the flags, so it can be replaced by a vision or multi-modal detector.

### Geometry-aware Adaptive Downsampling

The voxel size is adjusted online by proportional feedback so that the number of valid points in the current frame $$N_t$$ stays close to the target $$N_{target}$$.

$$v_{t+1} = v_t \left(\frac{N_t}{N_{target}}\right)^{1/\alpha}, \quad \alpha = 1.2$$

$$\alpha$$ is an effective dimension reflecting the nonlinear relation between point count and resolution, and the voxel size is clamped to $$[0.05, 0.8]$$m. In wide spaces the voxel is enlarged to save computation, and in the confined cabin it is reduced to preserve geometric detail.

### Mapping

An ikd-Tree (FAST-LIO2 family) global map is used. Local registration is done in $$F_E$$, and global insertion projects into $$F_W$$ by lifting by the elevator height.

## Experiments and Results

The system consists of pre-processing (buffering, static initialization, adaptive downsampling), state estimation + elevator mode management, and mapping. It is implemented in C++, supports both ROS1 and ROS2, and accepts Livox custom and standard ROS point clouds. The only sensor is a single Livox Mid360 (with integrated IMU), and a handheld platform was built with a Jetson Orin Nano and an industrial camera for recording visual context.

The data are a self-collected real-world dataset in 4 categories (Office/Dormitory/Campus/Mall), 20 sequences in total, with 79 elevator rides. It includes hard conditions such as mirror reflections, pedestrians, and long-distance vertical travel. The public benchmarks are Hilti 2022/2023, and a custom simulator was used in addition. The baselines are Point-LIO, FAST-LIO2, VoxelMap, and LIO-SAM; methods that do not support the Mid360 format or a 6-axis IMU were adapted with conversion nodes or community versions for a fair comparison. The computing environment is a desktop i5-14600KF/32GB, and real-time performance was also verified on a Jetson Orin Nano 4GB.

Since the real world has no complete ground truth, the metrics are the terminal vertical error ($$e_z = z_{end} - z_{ref}$$), same-floor revisits, and stairs-elevator cross-validation. For standard environments (Hilti), ATE RMSE against ground truth is used.

The three key results are as follows.

1. Clean sweep of the elevator scenarios (20/20 successes). All four baselines failed on every sequence. Point-LIO showed 20/20 Type-I drift (its strong dependence on the LiDAR prior prevents it from absorbing the elevator's vertical motion, overlaying point clouds from different floors onto the same map), VoxelMap and LIO-SAM showed 20/20 Type-II divergence, and FAST-LIO2 showed 11 drifts / 9 divergences. Elevator-LIO succeeded on all.
2. Terminal vertical error below 1cm (17/20 sequences). With the full system, most are at mm to cm level. Dormitory4 returned almost exactly to the starting height even after a round trip with 138.9m of cumulative vertical travel.
3. Competitiveness maintained in standard environments (Hilti 2022/2023). Adding the non-inertial capability does not hurt normal LIO performance. It is best/second-best on several sequences, and the best on Hilti2023 Underground1 at 0.013m. However, on some sequences such as Stair and Long Corridor it is behind at the 1m level, so it is not uniformly optimal.

| Method | Type-I drift | Type-II divergence | Success |
|---|---|---|---|
| Point-LIO | 20 | 0 | 0 |
| FAST-LIO2 | 11 | 9 | 0 |
| VoxelMap | 0 | 20 | 0 |
| LIO-SAM | 0 | 20 | 0 |
| Elevator-LIO | 0 | 0 | 20 |

In the ablation, the ZUPT (zero-state update) is decisive. The No-ZUPT variant sees vertical errors shoot up to meter level (Office4 -3.074m, Dormitory2 -2.31m). With Full, most are at mm level. The Map Reset variant (clearing and rebuilding the ikd-Tree at every floor arrival) removes the correction from revisiting the past map and better reflects the "pure non-inertial estimation capability"; the authors honestly interpret the mm-level error of Full as "the combined result of local non-inertial estimation + alignment with the past map", and state explicitly that it is not sub-cm absolute height accuracy over the entire run.

In the simulation robustness experiments, even with small/medium/large injections of initial gravity-alignment error, cabin tilt, and time-varying gravity drift, the return-to-origin height error remained bounded (worst case combined-large 0.312m). This supports the claim that the physical constraint of the stop event pulls in the model mismatch. Detector limitations were also measured. In the mirrored cabins of Campus4 and Mall1, multipath pseudo-structures caused the distance-threshold entry detection to fail, so those sequences were evaluated with manual triggers to separate them from the core estimator.

Checking the claimed contributions against what was actually delivered: the derivation of the decoupled equations of motion for the elevator non-inertial frame is sound but relies on simplifying assumptions such as ignoring cabin rotation and lateral motion. The claim that the ZUPT and lightweight detector suppress drift is strongly proven by ablation and is the core contribution. The feedback adaptive downsampling is reasonable, but its own quantitative ablation is weak and the differentiation from prior work such as AD-LIO is limited. The release of benchmark resources is only a promise and is currently not public. Since the comparison baselines are "off-the-shelf LIO without elevator handling", the clean sweep versus total failure is a somewhat predictable match, but the maintained competitiveness on Hilti, the simulation robustness, and the honest error interpretation give it credibility.

## Conclusion and Significance

This is the first systematic attempt to solve the almost unexplored problem of "continuous LIO inside a moving non-inertial carrier", minimally invasively on the FAST-LIO2 skeleton (state augmentation + mode switching + event-triggered ZUPT). The design that automatically reduces to standard LIO in normal indoor settings is practical. As the authors also state, it is a framework that can generalize to general moving bases such as subways, buses, ships, and aircraft, and it directly targets elevators, a practical failure point for multi-floor service robots (delivery, cleaning, logistics).

From a robotics practice perspective, the takeaways are as follows.

- Where it can be used: it applies directly to securing localization continuity during elevator rides in autonomous mobile-robot navigation in multi-floor indoor buildings. Real-time operation was verified with a single Livox Mid360 and a Jetson Orin Nano 4GB, so it is edge-friendly, and being ikd-Tree/IESKF based, its load is as light as FAST-LIO2.
- Re-implementation checkpoints: since the code is not public (to be released after publication, license undecided), re-implementation is needed for now. The algorithmic equations are relatively complete, so re-implementation on a FAST-LIO2 base is itself feasible. The key parts are the 18-dimensional augmented state and the per-mode $$\mathbf{F}_x$$/$$\mathbf{F}_w$$ on/off, the $$F_E$$ local point-to-plane residual, the exit ZUPT + re-anchoring + covariance reset, the 94th-percentile entry detection and the velocity-variance FSM exit detection, and the adaptive downsampling. Elevator data and trigger-threshold tuning are required, so reproduction difficulty is moderately high.
- Limitation 1, detector generality: distance-threshold entry detection fails in fully mirrored, transparent, or large freight elevators. Fusion with vision semantics, building IoT, or floor-plan priors is needed.
- Limitation 2, model simplification and weak observability: only the elevator's vertical translation is modeled and rotation and lateral disturbances are ignored, so it falls short on a rocking ship or bus. The absolute vertical position inside the cabin remains weakly observable, and the authors themselves admit that the mm-level terminal error is thanks to alignment with the past map.
- Limitation 3, global consistency and validation: with no loop closure or factor graph, long-term inter-floor map consistency is future work. High-precision 6-DoF ground truth inside a moving elevator could not be obtained, so absolute accuracy validation is limited.

## References

- Paper: [arXiv:2605.24495](https://arxiv.org/abs/2605.24495)
- Project page: [Elevator_LIO_Page](https://xiaofan4122.github.io/Elevator_LIO_Page/) (code not public)
- FAST-LIO2: Fast Direct LiDAR-Inertial Odometry (T-RO 2022) — source of the IESKF + ikd-Tree skeleton
- Point-LIO (Advanced Intelligent Systems 2023) — the main baseline in the elevator experiments
- Zero-velocity detection / ZUPT (Skog et al., TBME 2010) — theoretical root of the exit zero-state update
- VoxelMap (RA-L 2022) — a baseline and adaptive-voxel predecessor
- AD-LIO (IoT-J 2026) — direct predecessor of geometry-aware adaptive downsampling
