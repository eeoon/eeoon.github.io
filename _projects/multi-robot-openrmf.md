---
layout: page
title: Open-RMF 기반 이기종 다중로봇 중앙 관제
description: 대규모 실내 공간에서 수십 대 이동로봇을 하나의 관제 아래 묶는 협력 자율주행 시스템 — 30대 시뮬레이션에서 실물 4대 Sim-to-Real까지
img: assets/img/publication_preview/openrmf-cooperative-autonomous.png
importance: 1
category: company
related_publications: true
---

**기간** 2023.05 ~ 현재 · **소속** KETI 지능로보틱스연구센터 · **역할** 다중로봇 시뮬레이션·관제 시스템 설계 및 실증

## 배경과 문제

대규모 실내 업무공간에서 서로 다른 제어 체계를 가진 수십 대의 이동로봇이 함께 움직이려면, 중앙에서 임무를 배분하고 충돌 없이 운용할 관제 아키텍처가 필요하다. 다수 로봇을 동시에 가동할 때 전역 경로 간섭과 서버 통신 병목이 핵심 난제였다. 이 프로젝트는 [Open-RMF](https://www.open-rmf.org/)를 기반으로 이기종 로봇을 하나의 관제로 묶고, 시뮬레이션에서 검증한 로직을 실물 로봇에 옮기는 전 과정을 다룬다.

## 담당 업무

- **다중로봇 시뮬레이션 기반 구축:** Gazebo에서 TurtleBot3 → TETRA_S/TETRA_M 모델로 확장하며 namespace·TF 설계, Nav2 다중로봇 구성, ROS 1 → ROS 2(Foxy/Humble) 마이그레이션, 센서(LiDAR·IMU·RealSense) 드라이버 정리.
- **Open-RMF 관제·Task 시스템:** traffic_editor nav_graph 제작, fleet_adapter·free_fleet 연동, 사용자 입력 기반 go_to_place dispatch, 무한 반복 임무 자동 부여 노드(RMF Infinity Task), 배터리 플러그인 기반 충전 Task.
- **협력적 인지·주행:** 개별 로봇이 2D LiDAR·RGB-D(YOLOv8)로 탐지한 장애물을 전역 맵 좌표로 변환해 서버에 공유하고, 장애물이 교차하는 차선을 폐쇄(Lane Closed)해 다른 로봇이 시야 밖 장애물까지 선제 우회하도록 구현.
- **통신 최적화:** 실물 운용 시 통신 병목·데이터 손실을 CycloneDDS 설정으로 해소하고, 다른 도메인 로봇 연결은 Zenoh 브리지로 확장. WiFi-6E 환경에서 image_transport 기반 관제 실증.
- **경로계획 분석:** MAPF의 중앙집중(CBS) 및 분산(VO·RVO·ORCA) 알고리즘 구조 분석과 Gazebo 검증.
- **작업 이양·통합테스트:** 상태 기반 Replan Trigger ROS 2 서비스, ROS 2 긴급정지 토픽, 서버 중심 TF 재구성, 연차별 통합테스트 및 공인시험 성능지표 정의·데이터 수집.

## 결과

- **30대 규모** 다중로봇 시뮬레이션(RMF Gazebo World 변환·LiDAR 회피·YOLO 장애물 공유)으로 관제 로직을 검증한 뒤, **실물 4대**에서 교착 없이 임무를 수행하는 Sim-to-Real 실증.
- 협력적 장애물 정보 공유로 비협력 시스템 대비 충돌 위험·경로 비용 감소를 4대 로봇 실험으로 확인 (ICCAS 2024).
- 관련 논문: KROC 2024 {% cite kim2024openrmf %}, ICROS 2024 {% cite kim2024crossutilization %}, ICCAS 2024 {% cite kim2024traversability %} — 모두 제1저자.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/publication_preview/openrmf-cooperative-autonomous.png" title="Open-RMF 구조" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Full Control Fleet Adapter ↔ ROS 2 Free Fleet Server ↔ Free Fleet Client(ROS 1/ROS 2 모바일 로봇) 구조 (KROC 2024).
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/publication_preview/cooperative-path-planning-obstacle-info.png" title="장애물 탐지" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/publication_preview/traversability-assessment-path-planning.png" title="Multi-robot framework" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    왼쪽: 2D LiDAR 샘플링 + RGB-D YOLOv8 장애물 탐지 (ICROS 2024). 오른쪽: 로봇(State/Navigation/Object Detection) ↔ RMF(Task/Traffic/Schedule) 프레임워크 (ICCAS 2024).
</div>

## 기술 스택

Open-RMF · ROS 2 (Foxy/Humble) · Nav2 · Cartographer · Gazebo · CycloneDDS / Zenoh · YOLOv8 · TETRA_S / TETRA_M · C++ / Python
