---
layout: page
title: 이종 물류로봇 Open-RMF 통합 (협업지능)
description: TETRA 대차·스태커 AMR·MiR 600 등 이종 모바일로봇을 Open-RMF 멀티로봇 운영체계로 묶고, free_fleet 개조·도킹 제어권 핸드오버·센서 인식·PySide6 GUI를 단계적으로 구축
img: assets/img/publication_preview/openrmf-cooperative-autonomous.png
importance: 9
category: company
related_publications: true
---

**기간** 2023.05 ~ 2025.05 · **소속** KETI (협업지능 기반 로봇플러스 경쟁력 지원 사업) · **역할** 멀티로봇 운영체계 구축, free_fleet 개조·좌표 정합, 도킹 제어권 핸드오버, 센서 기반 장애물 인식, 외부 시스템 통신 연동, 시뮬레이션·GUI 도구 개발

## 단계별 진행

1. **Open-RMF·free_fleet 코어 (2023):** traffic_editor로 nav_graph 제작, 커스텀 로봇 model plugin(slotcar) 추가, 다중로봇 트래픽·태스크·시각화 검증 체크리스트, free_fleet 분석과 traffic_editor↔Nav2 맵 좌표계 정합, 실로봇 TETRA_DSV_M 연동(Cartographer 매핑 + ros1_bridge + free_fleet), free_fleet ROS 1 ClientNode 일시정지·도착 토픽 개조.
2. **도킹·장애물 회피·제어권 핸드오버 (2023 하반기):** TETRA 대차 도킹 시나리오, rmf_obstacle_detectors·lane_blocker 연동, 도킹 중 Open-RMF 제어권을 넘기고 되돌리는 상태기계(Moro_controller) 설계, ROS 2 joy teleop.
3. **매핑·센서 인식·실증 (2024):** namespace 기반 Cartographer, PCD → PGM 변환(3D → 2D 맵), LiDAR 동적 장애물 검출, 카메라 YOLO 객체 검출과 3D 좌표 변환, YOLOv8 장애물 인식 RealSense D435 실증.
4. **외부 시스템 통신 (2024 하반기):** 스태커 AMR 운용(MCP·VehiclePlanner·ACS·QR 정밀 도킹), 스태커↔ACS [TCP/IP 통신 프로토콜](/projects/agv-manipulator-tcpip/), MiR 600 사용법·프로그래밍.
5. **시뮬레이션·통신망·GUI 고도화 (2024~2025):** 배터리 시스템·LiDAR 장애물 회피 시뮬레이션, Isaac Sim 예제와 Open-RMF 연동 검토, MQTT 통신망 기초(Mosquitto), Gazebo Classic → Gazebo Sim 이전, RViz2 URDF·TF 시각화, Qt Designer·PySide6 + rclpy 통합 GUI.

## 결과

- 실로봇 TETRA_DSV_M의 Open-RMF 연동과 YOLOv8 장애물 인식 실증.
- 이 과제의 Open-RMF 협력 자율주행·장애물 정보 교차 활용 주제가 KROC 2024 {% cite kim2024openrmf %} / ICROS 2024 {% cite kim2024crossutilization %} 논문으로 이어졌고, PySide6 관제 GUI는 이후 [이상탐지 Fault Injection GUI](/projects/multi-robot-anomaly-detection/)의 기반이 되었다.

## 기술 스택

Open-RMF · free_fleet · Nav2 · ROS 2 / ROS 1 (ros1_bridge) · Cartographer · Gazebo (Classic / Sim) · Isaac Sim · YOLOv8 · RealSense D435/D455 · TETRA 대차 · 스태커 AMR · MiR 600 · PySide6 / Qt · MQTT
