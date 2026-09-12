---
layout: page
title: 4족 Wheel-Leg 하이브리드 로봇 제작 (메카톤 · 캡스톤)
description: 평소에는 바퀴로 주행하다 바퀴로 넘지 못하는 장애물에서 다리 관절을 제어해 보행 모드로 전환·극복하는 하이브리드 로봇. 기구 설계·3D 모델링·ROS 통합 제어 담당
img: assets/img/projects/wheel-leg-robot/img-3.png
importance: 2
category: personal
---

**기간** 2021.12 ~ 2022.06 · **소속** 계명대학교 로봇공학과 (메카톤, 캡스톤 우수상) · **역할** 아이디어 제안, 하드웨어 설계·3D 모델링, ROS 기반 주행 모드 제어

## 배경과 목표

주행 로봇은 평지에서 빠르고 효율적이지만 단차에 약하고, 보행 로봇은 지형 적응성이 좋지만 느리고 제어가 복잡하다. 두 이동 메커니즘을 하나의 플랫폼에 탑재하고, 다리 관절을 능동 제어해 상체 리프팅·자세 제어까지 가능하게 하는 것이 목표였다.

## 접근

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/wheel-leg-robot/img-1.png" title="D-H 파라미터" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/wheel-leg-robot/img-2.png" title="하드웨어 설계도" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/wheel-leg-robot/img-3.png" title="3D 모델" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">다리 관절 D-H 파라미터 분석(왼쪽), 전체 하드웨어 설계도(가운데), 로봇 3D 모델(오른쪽).</div>

1. **기구학 해석:** 다리 관절을 D-H 파라미터(α, a, d, θ)로 모델링해 순기구학으로 바퀴가 달린 다리 끝 위치를 계산하고, 역기구학으로 목표 위치에 대한 관절각을 역산했다.
2. **보행/주행 모드 전환:** 센서로 장애물 인식 → 보행 모드 진입 판단 → 액츄에이터로 다리 들어올림 → 목표 위치로 이동 → 통과 후 주행 모드 복귀.
3. **ROS 통합 제어:** 센서 데이터 수집 / 의사결정 / 액츄에이터 제어를 노드로 모듈화하고 토픽으로 비동기 통신해, 센서·알고리즘 추가 시 해당 노드만 교체하는 확장성을 확보했다.
4. **HW 설계·3D 모델링:** 수직 조절이 가능한 몸체 프레임과 각 다리의 바퀴 결합 구조를 CATIA로 설계·간섭 검증하고 3D 프린팅으로 제작했다. 토크 기반 모터 선정과 전류 용량 계산으로 배터리를 구성했다.

## 결과

"장애물 인식 → 액츄에이터 작동 → 로봇 리프팅 → 회피 주행"으로 이어지는 자율 동작 시퀀스를 ROS 기반으로 구현했고, 환경 변화에 능동적으로 자세를 바꾸며 주행하는 하이브리드 플랫폼을 완성했다. 이때의 전장·기구 경험은 이후 실로봇 전기 트러블슈팅의 기초가 되었다.

## 기술 스택

ROS (노드/토픽) · D-H 파라미터 · Forward / Inverse Kinematics · CATIA · 3D 프린팅 · 센서·액츄에이터 제어 · 모터·배터리 선정
