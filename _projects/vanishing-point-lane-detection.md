---
layout: page
title: 영상처리 기반 자율주행 — Vanishing Point × Perspective Transform
description: 소실점(직진/회전 판별)과 원근 변환(중앙/치우침 판별)을 결합해 4가지 주행 상태를 판단하는 저비용 영상처리 ADAS 프로젝트
img: assets/img/projects/autonomous-driving-image-processing/img-3.png
importance: 6
category: personal
---

**기간** 2022.03 ~ 2022.06 · **소속** 계명대학교 (개인 프로젝트) · **역할** 알고리즘 설계·구현 전반

## 배경과 목표

영상에서 검출된 차선이 만나는 소실점(Vanishing Point)을 활용해 조향 방향을 결정하는 자율주행 보조 시스템. 소실점만으로 주행 방향을 결정할 때 발생하는 오차를 차선 검출로 보완하고, 두 기법의 상호 보완으로 새로운 주행 상태 해석을 도입해 머신러닝 없이 저비용 ADAS를 구현하는 것이 목표였다.

## 접근

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/autonomous-driving-image-processing/img-3.png" title="Vanishing Point" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/autonomous-driving-image-processing/img-4.png" title="Perspective Transform" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">실제 영상의 Vanishing Point 검출(왼쪽, 빨간 점)과 Perspective Transform 적용 전/후(오른쪽).</div>

- **Vanishing Point:** 3D 평행선이 FOV 때문에 2D 영상에서 한 점에서 만나는 원리. Edge Detection → Hough Transform → 직선 검출 → 교점. Bounding Box로 소실점 범위를 규정하고(Gaussian 분포로 박스 타당성 검증) 박스 IN = 직진, OUT = 회전 구간으로 판별.
- **Perspective Transform:** 직선 구간에서 4개 좌표를 선정해 원근 변환 → Bird's Eye View 생성, ROI를 차선으로 변경. 두 라인이 평행이면 Parallel(중앙 주행), 비평행이면 Bias(치우침). FOV 45° vs 90° 비교 테스트로 시작.
- **결합 판단:** VP(IN/OUT) × PT(Parallel/Bias)의 4분류로 "직진 중앙 / 직진 치우침 / 회전 중앙 / 회전 치우침"을 판단하고 영상 오버레이 + 콘솔로 출력.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/autonomous-driving-image-processing/img-7.png" title="Parallel/Bias" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/autonomous-driving-image-processing/img-8.png" title="결합 매트릭스" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/autonomous-driving-image-processing/img-9.png" title="출력" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">차선 변환 + Parallel/Bias 판단(왼쪽), VP × PT 결합 판단 매트릭스(가운데), 영상·콘솔 출력(오른쪽).</div>

## 결과

Bounding Box로 직진/회전 구간을, Perspective Transform으로 차량 치우침을 판단하고 두 조합의 상호 보완으로 주행 안정성을 향상시키는 기법을 제안했다. 공개 주행 영상 데이터셋으로 검증했으며, 기존 ML 기반 자율주행과 결합 시 안정성 향상이 기대된다. 구현 과정은 [기술 노트](/blog/2021/vanishing-point-lane-detection/)에 정리했다.

## 기술 스택

OpenCV · Python / C++ · Hough Transform · Perspective Transform (Homography) · Gaussian Distribution · Confusion Matrix 응용
