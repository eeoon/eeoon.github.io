---
layout: about
title: about
permalink: /
subtitle: Mobile Robotics Engineer · <a href="https://www.keti.re.kr" target="_blank">KETI</a> 한국전자기술연구원 · Seoul, Korea

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false # crops the image to make it circular
  more_info: >
    <p>김영언 (Youngeon Kim)</p>
    <p>한국전자기술연구원 지능로보틱스연구센터</p>
    <p>"어제보다 나은 오늘"</p>

selected_papers: true # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page

announcements:
  enabled: true # includes a list of news items
  scrollable: true # adds a vertical scroll bar if there are more than 3 news items
  limit: 6 # leave blank to include all the news in the `_news` folder

latest_posts:
  enabled: true
  scrollable: true # adds a vertical scroll bar if there are more than 3 new posts items
  limit: 4 # leave blank to include all the blog posts
---

제조·물류·서비스 현장의 **이기종 멀티로봇을 하나의 관제 아래 통합하고 안정화하는** 시스템 통합·미들웨어·시뮬레이션 엔지니어입니다. 2023년부터 한국전자기술연구원(KETI)에서 [Open-RMF](https://www.open-rmf.org/) 기반 다중로봇 관제, 로봇–엘리베이터 연동, LiDAR 마커 정밀 도킹, Docker 기반 OTA 배포, 학습 기반 이상탐지, 실외 비정형 지형 자율주행을 시뮬레이션에서 실로봇까지 설계·실증해 왔습니다.

관심 분야는 **다중로봇 협력 자율주행, 대규모 fleet의 Sim-to-Real 검증, LiDAR-Inertial-Visual 위치추정, 사족보행 로봇의 실내 다층 이동, VLM/VLA 기반 상위 자율성**입니다. 오픈소스와 공개 연구를 현장 로봇에 이식·검증하는 일을 가장 즐깁니다.

#### 핵심 역량

| 영역 | 내용 |
| --- | --- |
| **멀티로봇 관제** | Open-RMF 기반 이기종 다중로봇 관제 — 임무 할당·충돌 회피·실시간 재계획, 30대 시뮬레이션 → 실물 4대 Sim-to-Real 실증 |
| **통신 인터페이스** | TCP/IP 커스텀 프로토콜(AGV–매니퓰레이터), MQTT–ROS 2 브리지(엘리베이터), ROS 2–JS(웹 관제), CycloneDDS/Zenoh 최적화 |
| **배포·유지보수** | Docker·Git 기반 OTA 프레임워크, Robot Profile 파라미터 분리, 무중단 서비스 모듈 교체 (SDR) |
| **자율주행·인식** | Nav2 · SLAM · LIO(FAST-LIVO2) · ICP 정밀 도킹(±3~5 cm) · grid_map 기반 traversability · SOR 센서 전처리 |
| **이상탐지·시각화** | Fault Injection GUI, 룰 기반 → Transformer 자기지도(RESTAD) 이상탐지, 웹 대시보드 |
| **시뮬레이션** | Gazebo(Classic/Harmonic), Isaac Sim/IsaacLab, MuJoCo — 다층 건물·사족보행·30대 fleet 테스트베드 |
| **툴** | ROS 2/ROS · C++/C · Python · MATLAB · Docker · Git · PySide6 · Linux |

#### 이 사이트에서 볼 수 있는 것

- [Projects](/projects/) — KETI에서 담당한 프로젝트와 입사 전 학부·인턴 프로젝트
- [Publications](/publications/) — 국제·국내 학회 논문 6편, 특허 1건
- [Blog](/blog/) — 최근 읽은 논문 리뷰(`paper-review`)와 기술 노트(`tech-note`)
- [CV](/cv/) — 학력·경력·기술 스택 요약
