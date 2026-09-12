---
layout: page
title: 실외 비정형 지형 자율주행 파이프라인
description: 3D Mesh Navigation 분석·검증에서 출발해 FAST-LIVO2 매핑·측위 → grid_map 지형 평가 → 전역 계획 → Pure Pursuit-LOS 추종의 자체 4단 파이프라인으로 발전시킨 실외 주행 스택
img: assets/img/projects/outdoor-traversability-autonomous/img-3.jpg
importance: 7
category: company
---

**기간** 2025.12 ~ 현재 · **소속** KETI (다중협력주행 4차년도 실외 확장 · SDR) · **역할** Mesh Navigation 분석·시뮬 검증, Open3D 포인트클라우드 보정, FAST-LIVO2 스택 구성, grid_map traversability·코스트맵 구현, 경로 추종 컨트롤러 설계

## 배경과 문제

2D Occupancy Grid는 평면 실내에는 효과적이지만 경사·단차·요철을 표현하지 못해 traversability 정보가 사라진다. 실외 비정형 지형에서 안전하게 주행하려면 지형을 3D로 표현하고, 그 위에서 위치추정·경로계획·추종을 이어야 한다.

## 접근 A — 3D Mesh Navigation (2025.12 ~ 2026.02, 분석·검증 완료)

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/outdoor-traversability-autonomous/img-1.jpg" title="Mesh Navigation" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">RViz2 Mesh Map 시각화 / Localization 정합 / Gazebo 시뮬레이션 환경.</div>

- **4단 파이프라인 분석:** Mapping(SLAM 점군 → Marching Cubes → Triangle Mesh) → Localization(MICP-L: 스캔↔메시 ICP) → Planning(CVP: 정점 Cost Layer 기반 연속 벡터장) → Controller(Move Base Flex 플러그인). Gazebo + RViz2에서 전 과정 연동 확인.
- **포인트클라우드 보정:** 수평 장착 3D LiDAR는 바닥면과 평행해 데이터가 부족하고 메시에 구멍이 생긴다. Open3D로 Z 필터링·ROI·SOR 전처리 → 3점 평면 방정식 → 평면 경계 내 격자점 생성(2.5 mm) → 경계 중복 처리 → Voxel Downsampling·법선 추정 → slam_to_mesh로 .ply 메시 출력.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/outdoor-traversability-autonomous/img-2.jpg" title="보정 전" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/outdoor-traversability-autonomous/img-3.jpg" title="보정 후" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">보정 전(왼쪽) 포인트클라우드 기반 메시와 보정 후(오른쪽) 메시 — 바닥면 밀도가 균일해지고 구멍이 거의 사라졌다.</div>

## 접근 B — 자체 4단 파이프라인 (2026 상반기 ~, 현행 주력)

| 단계 | 내용 |
| --- | --- |
| ① 매핑·측위 | **FAST-LIVO2**(LiDAR-IMU-Visual, sequential ESIKF) 아키텍처 분석 → ROS 2 Humble 스택 구성(포크 계보·2단계 빌드) → 자체 센서 적용으로 컬러 점군 지도 산출, fast_lio_localization으로 측위 |
| ② 지형 평가 | **grid_map** 기반 slope·roughness·step·traversable 4수식 파이프라인, FilterChain 상호검증, 실시간 TF |
| ③ 비용장·계획 | self-clearing 가우시안 비용장, 확률적 elevation mapping 경량 재구현(160k 셀 30 Hz, GPU 불필요), 전역맵 저장·로컬 코스트맵 |
| ④ 추종 | 자체 hybrid_controller — $$\omega = \omega_{pp} + \omega_{los}$$ (Pure Pursuit + LOS) 융합, 6-상태 머신, 손계산 정량 검증(최소 선회반경 0.67 m 등 구조 제약 도출) |

Mesh(연속 표면·CVP 벡터장)는 표현력이 높지만 스택이 무겁고, 2.5D grid_map은 경량·실시간이라 현재는 접근 B가 주력이다. 플랫폼은 Clearpath Jackal(LiDAR/IMU 마스트·NUC·micro-ROS 주행)이다.

## 결과

- Mapping → Localization → Planning → Controller 전체 파이프라인의 시뮬레이션 연동과 포인트클라우드 보정을 통한 실환경 데이터 결손 문제 해결.
- 접근 B 파이프라인 구성과 실내 실증 완료, 실외 테스트 진행 중.

## 기술 스택

FAST-LIVO2 · fast_lio_localization · grid_map / elevation mapping · Pure Pursuit · LOS Guidance · Mesh Navigation (MICP-L, CVP, MBF) · Open3D · slam_to_mesh · ROS 2 Humble · Gazebo / RViz2 · Livox Mid-360 · Clearpath Jackal · C++ / Python
