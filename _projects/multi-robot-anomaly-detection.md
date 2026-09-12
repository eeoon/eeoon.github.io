---
layout: page
title: 다중로봇 이상탐지 — Fault Injection에서 학습 기반 탐지까지
description: 30대 시뮬레이션 fleet에 결함을 주입해 데이터를 모으고, 룰 기반 4분류 이상탐지·웹 대시보드에서 Transformer 자기지도(RESTAD) 학습 기반 탐지로 발전시킨 연구
img: assets/img/publication_preview/anomaly-detection-visualization-rmf.png
importance: 4
category: company
related_publications: true
---

**기간** 2025.03 ~ 2026.05 · **소속** KETI (다중협력주행 과제) · **역할** Fault Injection·로깅 시스템 구축, 이상탐지 프레임워크 설계, 모델 학습·평가, 웹 시각화

## 배경과 문제

다중 로봇이 밀집된 환경에서는 개별 로봇의 작은 결함(배터리 급감, 위치추정 실패, 주행 제어 실패)이 fleet 전체의 병목·충돌로 전이될 수 있다. 그러나 기존 RMF는 개별 로봇 상태 시각화에 초점이 맞춰져 로봇 간 의존성이나 협업 실패를 진단할 수단이 부족했고, 임계치 기반 탐지는 실제 이상 데이터의 불규칙성을 잡아내기 어려웠다.

## 접근

### 1단계 — Fault Injection과 데이터 수집

- 시뮬레이션 로봇에 하드웨어(배터리·휠), Localization Lost, 장애물, 센서(LiDAR·카메라) 결함을 주입·복구하는 **PySide6 Fault Injection GUI**를 구축했다.
- 다중 로봇 정보를 ROS 2 통합 CSV로 로깅하는 시스템을 만들고(30대·10 Hz → 1 Hz 다운샘플), 이후 학습 데이터로 사용했다.

### 2단계 — 룰 기반 이상탐지와 웹 시각화 (ICCAS 2025) {% cite kim2025anomaly %}

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/publication_preview/anomaly-detection-visualization-rmf.png" title="Anomaly framework" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Anomaly Detection Framework — RMF(Task Allocation) → Anomaly State(4 types) → Web Dashboard → Operator.</div>

- 이상을 **개별 로봇 수준**(Battery Depletion, Obstacle Detection)과 **fleet 수준**(Trajectory Conflict, Collision Risk — 다른 로봇 경로 0.2 m 이내 교차)의 4가지로 정의했다.
- 작업 할당은 RMF가 담당하고 이상 탐지는 센서·로그 기반의 분리 모듈이 담당하는 모듈러 구조로, WebSocket/ROS Bridge 기반 웹 대시보드에서 운영자에게 실시간 알림·시각화했다. Gazebo 30대 환경에서 검증.

### 3단계 — 학습 기반 이상탐지 (ICROS 2026) {% cite kim2026learning %}

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/publication_preview/learning-anomaly-detection-framework.png" title="10대 시뮬레이션" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">Open-RMF 기반 10대 로봇 시뮬레이션 환경 (Gazebo + RMF 시각화).</div>

- 각 로봇의 시계열을 4차원 feature $$X_t = [P_t, V_t, B_t, R_t]$$(위치 오차·속도·배터리·임무 진행률)로 정규화했다.
- LSTM 시계열 예측과 Transformer AutoEncoder 재구성 기반 탐지를 거쳐, Transformer에 RBF 계층을 내장한 자기지도 모델 **RESTAD**를 채택해 라벨 없이 다중 로봇 간 시공간 상관관계를 학습했다.
- 실시간 ROS 2 스트리밍 파이프라인(Motion Residual 기반)으로 실로봇에 적용했다.

## 결과

| 단계 | 성과 |
| --- | --- |
| 룰 기반 (ICCAS 2025) | 4가지 이상 실시간 탐지·시각화, 30대 시뮬레이션 검증, 제1저자·IEEE Xplore |
| 학습 기반 (ICROS 2026) | Region-level **Aff-F1 0.94** — LSTM·일반 Transformer 능가, 제1저자 |
| 운영 가시성 | PySide6 통합 관제 GUI + Fault Injection GUI + 웹 대시보드 |

## 기술 스택

Open-RMF · ROS 2 · Gazebo · PySide6 / rclpy · WebSocket / ROS Bridge · PyTorch (LSTM, Transformer AE, RESTAD) · CSV 로깅
