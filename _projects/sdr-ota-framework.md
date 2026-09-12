---
layout: page
title: SDR — Docker·Git 기반 OTA 배포 프레임워크
description: Software-Defined Robot을 위해 로봇 기능을 컨테이너로 모듈화하고, 공통 코드 + Robot Profile 분리로 여러 로봇에 무중단 배포·검증하는 OTA 체계
img: assets/img/publication_preview/ota-update-framework-ros2-docker.png
importance: 3
category: company
related_publications: true
---

**기간** 2025.01 ~ 현재 · **소속** KETI (SDR 도메인 서비스 BM 개발 및 로봇 구현·검증 과제) · **역할** OTA 배포 프레임워크 설계·구현, Robot Profile 구조 설계, 기능별 검증

## 배경과 문제

현장 로봇이 늘고 기능이 바뀔 때마다 로봇마다 코드를 수정·재설치하면 유지보수가 불가능하다. 스마트폰이나 SDV(Software-Defined Vehicle)처럼 출시 후에도 소프트웨어 업데이트로 기능을 확장하는 **SDR(Software-Defined Robot)** 패러다임을 위해, 하드웨어 의존을 낮추고 원격으로 소프트웨어를 갱신하는 체계가 필요했다.

## 접근

<div class="row justify-content-sm-center">
    <div class="col-sm-9 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/publication_preview/ota-update-framework-ros2-docker.png" title="SDR OTA 아키텍처" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">SDR OTA 전체 아키텍처 — Public Cloud + Isolated Network 이중 구조, Dashboard·Simulator·Marketplace·State/Usage Logger, Docker Registry, Git Repository (ICCAS 2025).</div>

- **컨테이너 모듈화:** Docker·Git 기반으로 로봇 기능(Navigation, Patrol, Elevator, Docking)을 컨테이너 단위로 나누고, 백업 → 정지 → 클론 → 재기동을 자동화하는 OTA 스크립트를 구성했다. 로봇 PC에는 Host OS + Docker Engine만 두고 ROS 2 컨테이너를 순차 배포한다.
- **공통 코드 + Robot Profile 분리:** 같은 이미지로 배포해도 로봇마다 다른 주행·탑승·도킹 거동을 갖도록, 로봇별 차이를 외부 볼륨의 Robot Profile(YAML)로 분리했다. 코드와 파라미터의 수명주기를 분리한 것이 핵심이다.
- **이중 서버:** 공용 클라우드 OTA 서버와 격리 네트워크용 On-Premise 서버를 모두 지원하는 구조를 설계하고, 디버깅용 State Logger와 과금·구독 관리용 Usage Logger를 추가했다.
- **단계별 검증:** OTA 배포 후 Navigation·Patrol(2026.02), Elevator·Docking(2026.03) 기능을 코드 수정 없이 검증했다.

## 결과

- 소스 수정 없이 서비스 모듈(순찰·도킹·엘리베이터)을 교체·관리하는 **무중단 배포 구조**를 실로봇(TETRA_S)에서 검증.
- 초기 설치와 업데이트를 수행하는 셸 스크립트 2종으로 PoC를 완료하고 ICCAS 2025에서 발표했다(제2저자) {% cite kim2025ota %}.
- 이 배포 체계 위에서 [엘리베이터 연동](/projects/robot-elevator-interface/)과 [LiDAR 마커 도킹](/projects/lidar-marker-docking/) 기능이 운용된다.

## 기술 스택

Docker · Git / GitHub · ROS 2 Humble · Robot Profile (YAML, 외부 볼륨) · Bash · DDS · TETRA_S / Jackal
