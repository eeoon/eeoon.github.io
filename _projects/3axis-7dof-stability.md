---
layout: page
title: 3축 7자유도 Spring-Damper 시스템 안정성 평가와 PID 설계
description: 1축 3자유도 진동계에서 3축 7자유도로 확장하며 28×28 통합 상태공간을 구성하고, 무제어 시 불안정한 시스템을 PID로 안정화한 MATLAB 제어 프로젝트
img: assets/img/projects/3axis-7dof-stability/img-4.png
importance: 3
category: personal
---

**기간** 2021.02 ~ 2021.08 · **소속** 계명대학교 (학부연구생, Robot Dynamics and Intelligent Control Lab) · **역할** 수학 모델링, 상태공간 설계, MATLAB 안정성 시뮬레이션, PID 제어기 설계

## 배경과 목표

로봇 몸체가 외력을 받아도 평형 상태로 복귀해 다음 외력에 대비하는 자율 안정화(외란 거부) 시스템이 최종 목표였다. 단순 1축 모델에서 시작해 점진적으로 3축 시스템으로 확장하며, 복잡도가 안정성에 미치는 영향을 정량적으로 확인했다.

## 접근

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/3axis-7dof-stability/img-1.png" title="3자유도 모델링" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/3axis-7dof-stability/img-2.png" title="계단 응답" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">1축 3자유도 운동방정식·모델링(왼쪽)과 MATLAB 계단 응답(오른쪽).</div>

1. **1축 3자유도 모델링:** 질량체 m₁, m₂, m₃가 스프링(k)·댐퍼(c)로 직렬 연결된 구조의 운동방정식을 도출했다.
2. **1차 안정성 검증:** m₁에 455 N 외력을 가했을 때 중심 m₂의 위치 변화를 추종. 제어기 없이도 계단 응답에서 진폭이 감쇠하며 안정성이 유지됨을 확인했다.
3. **3축 7자유도 확장:** 중심 질량체(m₇)에 6개 질량체(m₁~m₆)가 방사형으로 배치된 구조로 확장했다.
4. **상태공간 통합:** 각 축의 운동방정식을 $$\dot{x} = Ax + Bu,\; y = Cx + Du$$로 변환하고 두 14×14 시스템을 블록 대각으로 묶어 **28×28 통합 행렬**을 구성, 단일 환경에서 동시 해석했다.
5. **PID 설계:** 3축 확장 후 무제어로는 안정성이 확보되지 않음을 확인하고, 각 축에 PID를 설계해 게인을 시뮬레이션으로 도출했다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/3axis-7dof-stability/img-3.png" title="7자유도 구상도" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/3axis-7dof-stability/img-4.png" title="7자유도 모델링" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/3axis-7dof-stability/img-5.png" title="상태변수 행렬" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">3축 7자유도 구상도(왼쪽), 모델링(가운데), 상태방정식의 상태변수 행렬(오른쪽).</div>

## 결과

1축에서는 확보되던 안정성이 3축 7자유도로 확장하면 무제어로는 확보되지 않는다는 사실을 시뮬레이션으로 발견하고, "문제 인식 → 분석 → PID 해결"의 엔지니어링 전 과정을 경험했다. 게인을 시뮬레이션으로 결정하는 체계적 방법론을 확립했다.

## 기술 스택

MATLAB (Control System Toolbox) · 운동방정식 / 상태공간 표현 · Spring-Damper 진동계 · PID 제어 · 선형대수
