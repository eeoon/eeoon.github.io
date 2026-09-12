---
layout: page
title: 6축 다관절 로봇 기구학 시뮬레이션 (D-H · 역기구학 · Cubic Spline)
description: 6자유도 협동로봇의 D-H 파라미터를 도출해 순기구학·역기구학·경로계획을 MATLAB으로 구현하고 수계산 결과와 시뮬레이션의 일치를 검증한 개인 프로젝트
img: assets/img/projects/6axis-robot-simulation/img-5.png
importance: 4
category: personal
---

**기간** 2021.09 ~ 2021.11 · **소속** 계명대학교 (개인 프로젝트) · **역할** 기구학 모델링부터 MATLAB 시뮬레이션 구현까지 단독 수행

## 배경과 목표

산업 협동로봇은 링크 간 관계를 정확히 수학적으로 모델링해야 End-effector를 의도한 위치에 보낼 수 있다. D-H 파라미터를 직접 유도·구현하며 기구학 전반을 이해하고, 수계산 값과 시뮬레이션 결과의 일치성을 검증하는 것이 목표였다.

## 접근

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/6axis-robot-simulation/img-1.png" title="D-H 파라미터" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/6axis-robot-simulation/img-2.png" title="천이행렬" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">좌표계 설정과 D-H 파라미터 정의(왼쪽), 천이행렬 유도(오른쪽).</div>

1. **좌표계 설정·D-H 도출:** 각 조인트 원점을 지정하고 회전축 기준 Z축, 오른손 법칙으로 X·Y축을 잡아 4개 파라미터(d, θ, a, α)를 정의했다.
2. **천이행렬 유도:** 조인트 간 변환행렬을 D-H로 일반화하고 $$T_0^n = A_0^1 A_1^2 \cdots A_{n-1}^n$$으로 베이스 → End-effector 관계를 도출했다.
3. **MATLAB 구현:** 변환행렬 함수, 링크/조인트 플롯, 초기 자세, 사용자 입력 누적 시각화를 함수·실행 파일로 분리한 구조로 구현했다.
4. **경로계획:** 직선 경로의 불안정성(정지 → 등속 → 정지 불가)을 보완하기 위해 출발·도착 각속도 0 조건의 Cubic Spline $$\theta(t) = A\left(1 - \cos\frac{n\pi}{T}t\right)$$를 적용했다.
5. **역기구학:** End-effector 위치로부터 각 θ를 역산. atan2로 −π~π 연속성을 확보하고, a = 0 변환행렬에서 분모가 0이 되는 경우를 특이점으로 분류했다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/6axis-robot-simulation/img-3.png" title="MATLAB 구현" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/6axis-robot-simulation/img-4.png" title="Cubic Spline" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/6axis-robot-simulation/img-5.png" title="검증" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">MATLAB 시뮬레이션(왼쪽), Cubic Spline 경로계획(가운데), 수계산 vs 시뮬레이션 일치 검증(오른쪽).</div>

## 결과

- θ₂ = 60°, θ₅ = 60° 입력 시 수계산 (X, Y, Z) = (117, 1170, 1127)과 시뮬레이션 결과가 완전히 일치해 모델 정확성을 검증했다.
- 사용자가 θ값을 입력해 자세 변화를 실시간으로 관찰하는 인터랙티브 툴을 완성했다.
- 남은 과제: 특이점 자동 검출·우회, Jacobian 기반 속도 제어, Simulink 연계.

## 기술 스택

MATLAB · D-H Parameter · Forward / Inverse Kinematics · Cubic Spline · 동차변환행렬
