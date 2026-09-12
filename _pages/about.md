---
layout: about
title: about
permalink: /
subtitle: Mobile Robotics Engineer · <a href="https://www.keti.re.kr" target="_blank">Korea Electronics Technology Institute (KETI)</a> · Seoul, Korea

# The profile block of the theme is intentionally not used here.
# The intro and the photo are laid out as two explicit columns below,
# so the text never wraps under the image.

selected_papers: true # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page

announcements:
  enabled: false # news timeline lives at /news/
  scrollable: true
  limit: 6

latest_posts:
  enabled: false # blog posts live at /blog/
  scrollable: true
  limit: 4
---

<div class="row">
  <div class="col-sm-8 mt-3 mt-md-0">
    <p>I am Youngeon Kim, a mobile robotics engineer at the Korea Electronics Technology Institute (KETI). Since 2023 I have been building and validating heterogeneous multi-robot systems, from simulation to deployment in real buildings and factories.</p>
    <p><strong>Research interests:</strong> multi-robot coordination and fleet-scale sim-to-real, LiDAR-inertial-visual localization and mapping, traversability for outdoor and multi-floor environments, legged robots inside buildings, and vision-language models as the decision layer for embodied autonomy.</p>
    <p>I enjoy turning open research into robust, well-documented systems that actually run on real robots, and I am always glad to talk with people working on similar problems.</p>
  </div>
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/prof_pic.jpg" class="img-fluid rounded z-depth-1" alt="Youngeon Kim" %}
    <div class="more-info" style="font-family: monospace; font-size: 0.85rem; margin-top: 0.5rem;">
      <p>Youngeon Kim (김영언)</p>
      <p>Intelligent Robotics Research Center, KETI</p>
      <p>"Better today than yesterday."</p>
    </div>
  </div>
</div>

#### Experience

| Period | Organization | Role |
| --- | --- | --- |
| 2023.05 – present | Korea Electronics Technology Institute (KETI), Intelligent Robotics Research Center, Seoul | Researcher, Mobile Robotics — multi-robot fleet management, autonomous navigation, SDR/OTA, simulation, anomaly detection |
| 2022.08 – 2023.03 | Korea Institute of Robotics and Technology Convergence (KIRO), Robot Vocational Innovation Center, Gumi | Intern — integrated training environment for 8+ heterogeneous industrial and collaborative robots |
| 2021.03 – 2022.01 | Robot Dynamics and Intelligent Control Lab, Keimyung University | Undergraduate researcher — multi-DOF control systems, MFC-based actuator control |
| 2017.03 – 2023.02 | Keimyung University, Daegu | B.S. in Robotics Engineering (GPA 4.2/4.5, major 4.4/4.5) |

#### Ongoing National R&D Projects

| Project | Period | My role |
| --- | --- | --- |
| [Cooperative mapping, environment recognition and autonomous driving for multiple mobile robots in large-scale indoor workspaces](/projects/multi-robot-openrmf/) | 2023.05 – present (4th year) | Open-RMF fleet management and task system, multi-robot simulation (30 robots) to real-robot deployment (4 robots), cooperative obstacle sharing, [fault injection and learning-based anomaly detection](/projects/multi-robot-anomaly-detection/), [outdoor traversability pipeline](/projects/outdoor-traversability/) |
| [Business model development and robot implementation/validation for Software-Defined Robot (SDR) domain services](/projects/sdr-ota-framework/) | 2025.01 – present | Docker/Git OTA framework with robot profiles, [robot–elevator multi-floor navigation](/projects/robot-elevator-interface/), [LiDAR-marker ICP docking](/projects/lidar-marker-docking/), [Go2 multi-floor simulation testbed and VLM mission agent](/projects/go2-multifloor-sim/) |
| [Collaborative-intelligence-based Robot Plus competitiveness support](/projects/logistics-fleet-integration/) | 2023.05 – 2025.05 (completed) | Heterogeneous logistics robots (TETRA, stacker AMR, MiR) under Open-RMF, [AGV–manipulator TCP/IP protocol](/projects/agv-manipulator-tcpip/), docking control hand-over, PySide6 supervisory GUI |

Full project write-ups are on the [projects](/projects/) page; the [CV](/cv/) page has the complete record.
