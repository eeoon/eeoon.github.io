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
    <p>I am Youngeon Kim, a mobile robotics researcher and engineer at the Korea Electronics Technology Institute (KETI).</p>
    <p><strong>Research interests,</strong> Multi-robot system and Sim-to-Real, LiDAR-inertial-visual localization and mapping, Traversability for outdoor and multi-floor environments, and vision-language models(VLM) as the decision layer for autonomy.</p>
    <p>I believe robotics has the potential to make our lives better by solving real-world problems in new ways. I'm passionate about exploring new ideas and turning them into practical robotic solutions. My goal is to keep growing as an engineer and contribute to making robots more useful in the real world.</p>
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
| 2022.08 – 2023.03 | Korea Institute of Robotics and Technology Convergence (KIRO), Robot Vocational Innovation Center, Gumi | Intern — integrated training environment for heterogeneous industrial and collaborative robots |
| 2021.03 – 2022.01 | Robot Dynamics and Intelligent Control Lab, Keimyung University | Undergraduate researcher — system modeling and stability design of piping robots, MFC-based actuator control |
| 2017.03 – 2023.02 | Keimyung University, Daegu | B.S. in Robotics Engineering (GPA 4.2/4.5, major 4.4/4.5) |

#### Ongoing Projects

**[Cooperative Multi-Robot Navigation in Large-Scale Indoor Workspaces](/projects/multi-robot-openrmf/)**<br>
<small>2023.05 – present</small>

- Open-RMF fleet management and task system; 30-robot simulation to 4 real robots
- Cooperative obstacle sharing and lane closure; [fault injection and learning-based anomaly detection](/projects/multi-robot-anomaly-detection/)
- Outdoor extension: [traversability navigation pipeline](/projects/outdoor-traversability/)

**[Software-Defined Robot (SDR) Domain Services](/projects/sdr-ota-framework/)**<br>
<small>2025.01 – present</small>

- Docker/Git OTA framework with robot profiles
- [Robot–elevator multi-floor navigation](/projects/robot-elevator-interface/) and [LiDAR-marker ICP docking](/projects/lidar-marker-docking/) on a real robot
- [Go2 multi-floor simulation testbed and VLM mission agent](/projects/go2-multifloor-sim/)

Full project write-ups are on the [projects](/projects/) page; the [CV](/cv/) page has the complete record.
