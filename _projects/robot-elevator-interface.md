---
layout: page
title: 모바일 로봇 ↔ 엘리베이터 연동 다층 자율주행
description: MQTT–ROS 2 브리지와 커스텀 NavigateMultiFloor Action으로 로봇이 스스로 엘리베이터를 호출·탑승·하차하는 시스템. 승강기 금속면 LiDAR 난반사는 SOR 필터로 해결
img: assets/img/projects/mobile-robot-elevator-interface/img-1.png
importance: 2
category: company
---

**기간** 2025.05 ~ 2025.09 · **소속** KETI (SDR 과제) · **역할** MQTT–ROS 2 브리지 설계·구현, 다층 이동 시퀀스 제어, 센서 전처리, 실증

## 배경과 문제

기존 로봇 자율주행은 단일 층 내비게이션 중심이다. 다층 건물 서비스를 위해서는 엘리베이터 연동이 필수지만 네 가지 장벽이 있었다. ① 엘리베이터는 MQTT, 로봇은 ROS 2로 통신 방식이 다르다. ② 실제 엘리베이터 실험은 반복성·안전·비용 제약이 크다. ③ 호출→탑승→층 이동→하차의 다단계 시퀀스를 상태 제어해야 한다. ④ 승강기 내부 금속·유리 면의 LiDAR 난반사가 위치추정을 무너뜨린다.

## 접근

<div class="row justify-content-sm-center">
    <div class="col-sm-9 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/mobile-robot-elevator-interface/img-1.png" title="시스템 아키텍처" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">전체 시스템 아키텍처 — ROS 2 로봇 / Bridge / MQTT 엘리베이터의 3계층 추상화.</div>

1. **MQTT↔ROS 2 Bridge (Phase 1):** 양방향 변환 Bridge Node로 로봇/브리지/엘리베이터를 3계층으로 추상화해 로봇 측은 MQTT를 직접 다루지 않게 했다. 상태는 Topic(ArriveResponse, CallRequest, CmdResult), 명령은 Service(CallElevator, ArriveElevator, RequestBoard/Unboard, UpdateBoardState)로 분리해 API를 정의했다.
2. **Gazebo 검증 (Phase 2):** Nav2 기본 Action은 단일 층만 처리하므로 커스텀 **NavigateMultiFloor** Action을 설계해 "현재 층 확인 → 호출 → 탑승 → 층 이동 → 하차 → 목적 좌표"를 단일 미션으로 통합 제어했다. Clearpath Jackal + 엘리베이터 플러그인이 있는 다층 Gazebo 월드에서 반복 검증.
3. **TETRA_S 실증 (Phase 3):** 실제 운용 중인 엘리베이터에 브리지를 연결하고 Nav2(NavFn/DWB) 기반 9개 상태(IDLE → 대기지점 이동 → 호출 → 도착 대기 → 탑승 → 내부 대기 → 하차 → 목적지) 흐름으로 전 과정을 수행.
4. **SOR LiDAR 전처리 (Phase 4):** 각 포인트의 K개 이웃 평균거리를 전체 분포(μ, σ)와 비교해 μ+α·σ를 넘는 고립 포인트만 제거하는 Statistical Outlier Removal로, 벽면 구조는 보존하고 튀는 반사만 선택적으로 걸러냈다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-2.png" title="다층 Gazebo 환경" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-3.png" title="Gazebo 실험" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">다층 Gazebo 환경의 Jackal 로봇과 엘리베이터 탑승 시뮬레이션 장면.</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-4.png" title="실증 1" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-5.png" title="실증 2" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">실제 건물 실증 — TETRA_S의 호출 / 탑승 / 내부 대기 / 하차.</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-7.png" title="SOR 적용 전" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mobile-robot-elevator-interface/img-8.png" title="SOR 적용 후" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">SOR 필터 적용 전(왼쪽)과 후(오른쪽)의 LiDAR 스캔 비교 — 고립 반사 포인트가 제거되고 벽면 윤곽이 연속적으로 유지된다.</div>

## 결과

| 항목 | 결과 |
| --- | --- |
| MQTT↔ROS 2 변환 성공률 | 양방향 **100%** |
| 평균 변환 지연 | **30~60 ms**, 다중 호출 환경에서 패킷 손실 없음 |
| 실증 | TETRA_S가 실제 엘리베이터에서 호출–탑승–대기–하차 전 과정을 안정 수행, 하차 후 Nav2 연속 주행, 경로 단절 없음 |
| 센서 | SOR 적용 후 고립 포인트 다수 제거, 장애물 인식이 실환경과 일치 |

이 프로젝트의 SOR 트러블슈팅은 이후 OTA 프레임워크 위에서 Elevator·Docking 기능을 코드 수정 없이 검증하는 [SDR OTA 프로젝트](/projects/sdr-ota-framework/)로 이어졌다.

## 기술 스택

ROS 2 Humble · MQTT (Mosquitto) · 자체 MQTT–ROS 2 Bridge · Nav2 (NavFn / DWB) · 커스텀 Action · 2D LiDAR / IMU / Encoder · SOR · Gazebo + Elevator Plugin · Jackal / TETRA_S · C++ / Python
