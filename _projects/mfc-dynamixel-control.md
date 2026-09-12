---
layout: page
title: MFC 기반 Dynamixel 액츄에이터 3모드 제어
description: C++ MFC GUI로 ROBOTIS Dynamixel XM430-W350의 위치·속도·전류 제어 모드를 통합 운용하고 TTL 통신으로 상태·토크를 실시간 모니터링
img: assets/img/projects/mfc-actuator-control/img-1.png
importance: 5
category: personal
---

**기간** 2021.08 ~ 2021.12 · **소속** 계명대학교 (학부연구생) · **역할** MFC GUI 설계·개발, 3모드 제어 구현, TTL 양방향 통신, 실시간 모니터링

## 배경과 목표

로봇의 핵심 구동부인 액츄에이터를 SDK 예제 수준을 넘어 직접 제어해 보는 것이 목표였다. Dynamixel의 3가지 작동 모드를 모두 프로그래밍하고, GUI 입력에 실시간으로 반응하면서 현재 상태(위치·속도·전류)를 모니터링하도록 구성했다.

## 접근

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/mfc-actuator-control/img-1.png" title="MFC 화면" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mfc-actuator-control/img-2.png" title="MFC 제작 과정" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">MFC 윈도우 화면과 제작 과정.</div>

1. **MFC GUI 설계:** Connection/Disconnection, Enable(토크 활성화), Present Value(현재 상태), Goal Value(목표 입력), Torque Value, MOVE 버튼으로 구성한 Windows 응용 프로그램.
2. **3가지 제어 모드:** 위치 제어(목표 각도, 관절), 속도 제어(목표 RPM 연속 회전, 바퀴), 전류 제어($$\tau = K_t I$$ 관계를 이용한 정밀 토크 제어 + 토크값 실시간 표시).
3. **TTL 통신:** PC USB → USB-TTL 변환기 → Dynamixel. Half-Duplex TTL 시리얼로 다수 액츄에이터를 데이지 체인 연결.
4. **실시간 모니터링:** Present Value를 읽어 Goal Value와 비교해 제어 정확도를 직관적으로 확인.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mfc-actuator-control/img-3.png" title="TTL 통신" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/mfc-actuator-control/img-4.png" title="XM430-W350" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">TTL 통신 방식(왼쪽)과 사용한 Dynamixel XM430-W350(오른쪽).</div>

## 결과

C++/MFC와 하드웨어가 직접 통신·제어하는 시스템 레벨 프로그래밍 경험을 확보했고, 위치·속도·전류 제어의 차이를 실제 하드웨어 응답으로 체득했다. 이 경험은 이후 [Wheel-Leg 로봇](/projects/wheel-leg-robot/)의 액츄에이터·센서 통합 제어의 기반이 되었다.

## 기술 스택

C++ · MFC · Dynamixel SDK · TTL Half-Duplex 시리얼 통신 · Visual Studio · ROBOTIS XM430-W350
