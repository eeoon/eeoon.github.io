---
layout: page
title: LiDAR 마커 ICP 정합 기반 정밀 충전 도킹
description: ArUco 비전 → 2D LiDAR 사다리꼴 기하 마커 → ICP 정합의 3단계로 발전시켜 실환경 ±3~5 cm 정밀 도킹을 달성한 2단계(Nav2 접근 + 미세 정렬) 도킹 시스템
img: assets/img/projects/lidar-marker-charging/img-6.png
importance: 5
category: company
---

**기간** 2025.10 ~ 2025.12 · **소속** KETI (SDR 과제) · **역할** 마커 인식 파이프라인 구현, LiDAR 기하 인식 알고리즘 설계, ICP 정합·파라미터 튜닝, Nav2 도킹 통합, TETRA_S 실증

## 배경과 문제

일반적인 LiDAR 자율주행은 목적지 인근까지 접근하는 데는 효과적이지만, 충전 도킹·지정 작업 위치·협소 공간 정차처럼 **수 cm 단위 정밀 정렬**에는 한계가 있다. 각 인식 방식의 장단점을 단계적으로 보완하며 실환경에서 동작하는 도킹 시스템을 만드는 것이 목표였다.

## 접근

### Phase 1 — ArUco 비전 도킹 (시뮬레이션)

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/lidar-marker-charging/img-1.png" title="ArUco 주차" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">ArUco 마커가 부착된 Gazebo 주차 환경과 마커 인식 결과.</div>

이진화 → 윤곽선/사각형 후보 → Homography 투시 보정 → ID 디코딩 → PnP로 6-DOF Pose → TF 변환(camera → base_link → odom → map). Nav2로 접근한 뒤 마커 좌표를 목표로 정밀 정차한다. 조명·노이즈·가림에 민감하다는 한계가 있었다.

### Phase 2 — 2D LiDAR 기하 마커 (시뮬레이션)

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/lidar-marker-charging/img-2.jpg" title="사다리꼴 마커" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">다양한 크기의 사다리꼴 마커 시뮬레이션 환경.</div>

카메라 없이 비대칭 사다리꼴(하단 60° / 상단 120°) 마커를 LiDAR 직선 분할과 각도·변 길이 비율로 판별했다. 각도 구조만 유지되면 크기가 변해도 인식되는 Scale Invariance를 검증했고, 마커가 클수록 포인트 밀도가 높아 정확도가 올라갔다.

### Phase 3 — ICP 정합 도킹 (실환경 실증)

사용자 정의 사다리꼴 템플릿 $$T$$와 실시간 스캔 $$S$$를 Point-to-Point ICP로 정합한다. KD-Tree 최근접 대응($$O(\log M)$$) → Centroid·교차공분산 $$H$$의 SVD로 최적 $$R = VU^{T}$$, $$t = \bar{t} - R\bar{s}$$ → 수렴 판정(Δt, Δθ, MSE) → fitness score로 성패 판정. Nav2 접근 후 저속 구간에서만 ICP를 활성화하고 KD-Tree 재활용·Downsampling으로 실시간성을 확보했으며, 도킹 파라미터(retries, abort_distance, docked_distance_threshold 등)는 반복 실험으로 확정했다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/lidar-marker-charging/img-3.png" title="정합 전" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/lidar-marker-charging/img-4.png" title="정합 후" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">ICP 정합 전(왼쪽)과 후(오른쪽) — 흰색 템플릿과 초록색 LiDAR 스캔.</div>

<div class="row mt-3">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/lidar-marker-charging/img-6.png" title="실제 도킹" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">실제 도킹 프레임과 주행 로봇 TETRA_S.</div>

## 결과

- Nav2 글로벌 접근 → ICP 정밀 정렬의 2단계 제어를 통합해 실환경 **±3~5 cm** 정밀 도킹 실증.
- 시뮬레이션 기반 배터리 충전 로직과 결합해 자동 충전 시나리오를 완성했고, 이후 OTA 프레임워크 위에서 Docking 기능을 코드 수정 없이 검증했다.

## 기술 스택

OpenCV ArUco · PnP · Line Segmentation · ICP · KD-Tree · SVD · Nav2 Docking Server · TF · ROS 2 Humble · Gazebo · PCL · Jackal / TETRA_S · C++ / Python
