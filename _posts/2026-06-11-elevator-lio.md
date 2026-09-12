---
layout: post
title: "Elevator-LIO: Robust LiDAR-Inertial Odometry for Multi-Floor Navigation under Elevator-Induced Non-Inertial Motion (arXiv 2026)"
date: 2026-06-11 09:00:00 +0900
description: 움직이는 엘리베이터 안에서 로봇 상대운동과 캐빈 수송운동을 상태에서 분리하고 정지 시점 ZUPT로 수직 드리프트를 잡는 멀티플로어 LIO
tags: [lio, elevator, multi-floor, navigation]
categories: paper-review
thumbnail:
giscus_comments: false
related_posts: false
toc:
  sidebar: left
---

| 항목 | 내용 |
|---|---|
| 저자 | Yifan Zhang, Yudong Huang, Yuchong Zhang, Changze Li, Haoran Liu, Ming Yang, Tong Qin (상하이교통대학 SJTU 계열) |
| 발표 | arXiv preprint 2026 (arXiv:2605.24495, 2026-05-23; 정식 학회/저널 venue는 확인되지 않음, 더블블라인드 심사 중) |
| 링크 | [arXiv](https://arxiv.org/abs/2605.24495) · [프로젝트 페이지](https://xiaofan4122.github.io/Elevator_LIO_Page/) — 코드/데이터셋/시뮬레이터는 출판 후 공개 예정으로 현재 미공개 |

## 한 줄 요약

로봇이 움직이는 엘리베이터에 타면 IMU 가속도에 엘리베이터의 수송운동이 섞여 들어가고, 이를 자기운동으로 오인한 기존 LIO는 발산하거나 심하게 드리프트한다. Elevator-LIO는 로봇의 상대운동과 엘리베이터의 수송운동을 상태에서 분리(decoupling)하고, 엘리베이터가 정지하는 순간의 물리적 구속을 ZUPT(영속도/영가속도 보정)로 활용해 수직 드리프트를 잡아낸다. FAST-LIO2 골격 위에 최소 침습적으로 얹은 구조라 일반 실내에서는 표준 LIO로 자동 환원된다.

## 연구 계보와 동향

- LIO 본류: LIOM에서 LIO-SAM(smoothing/factor-graph)과 FAST-LIO/FAST-LIO2(iterated ESKF + ikd-Tree)로 갈라졌고, Point-LIO, DLIO, Faster-LIO, VoxelMap으로 이어진다. 이 논문은 FAST-LIO2 계열의 IESKF + ikd-Tree 골격을 그대로 계승하고, 그 위에 엘리베이터 비관성 상태를 augment한 형태다.
- 약관측(weak-observability)과 이동 베이스 추정: Ground-Fusion/VIWO(휠·차량 운동학 구속), ZUPT(관성항법의 영속도 검출, Skog 2010), 다리 로봇의 접촉 구속(VILENS, Leg-KILO, LIKO). "간헐적 물리 구속이 약관측 하에서 드리프트를 억제한다"는 이 계열의 아이디어를 엘리베이터 정지 이벤트에 적용했다.
- 멀티플로어·엘리베이터 인지 SLAM: MuNES, NV-LIO 등은 층 전환 관리, 매핑, 내비게이션에 초점을 맞춘다. 저자의 주장은 "로봇이 물리적으로 움직이는 엘리베이터 안에 있는 동안의 연속 위치추정 자체를 정면으로 다룬 연구는 없었다"는 것이고, 이 빈 칸이 본 논문의 포지셔닝이다.
- 장면 스케일 적응: LVIO-Fusion(다중모달), AD-LIO(장면 열화 기반 adaptive voxel). 본 논문의 adaptive downsampling은 이 계열의 경량 변형이다.

참고로 arXiv 공식 제목은 위 표기와 같고, 일부 자료에서는 "A Robust LiDAR-Inertial Odometry in Non-Inertial Frames and Confined Spaces"라는 부제로도 통용된다. 동일 논문이다.

## 문제와 동기

표준 LIO는 "센서 플랫폼이 관성 좌표계에서 움직인다, 따라서 IMU 가속도는 로봇 자기운동(ego-motion)이다"를 전제한다. 로봇이 움직이는 엘리베이터 캐빈에 들어가면 이 가정이 붕괴한다.

1. 추정 관점: 측정 가속도에 엘리베이터 수송운동과 캐빈 대비 로봇 상대운동이 중첩된다. 분리하지 않으면 적분 오차가 누적된다. 게다가 닫힌 캐빈 안에서 LiDAR는 캐빈 내부 기하 대비 상대운동만 관측하므로, 절대 수직운동 상태가 관측 불가(unobservable)가 되어 공분산이 계속 커진다.
2. 프론트엔드 관점: 넓은 층과 협소한 캐빈 사이에서 장면 스케일이 급변한다. 고정 voxel 해상도로는 협소 공간에서 점이 과도하게 깎여 degeneracy나 실패가 나고, 넓은 공간에서는 점이 과잉되어 실시간성이 떨어진다.

결과적으로 기존 LIO는 엘리베이터 운동을 로봇 운동으로 오인해 심한 수직 드리프트, 맵 불일치, 발산을 일으킨다.

## 핵심 아이디어와 방법

### 비관성 상태 분리

상태를 엘리베이터 프레임 $$F_E$$ 대비 로봇 상태 $$\mathbf{x}_{rel}$$과 월드 대비 엘리베이터 수송 상태 $$\mathbf{x}_{elev}$$로 쪼갠다. 일반화된 상대운동 정리(transport, Coriolis, Euler, 구심 가속도 모두 포함)에서 출발하되, 엘리베이터 단순화 가정으로 줄인다. 캐빈 회전을 무시($$\boldsymbol{\omega}_E \approx 0$$)하고 엘리베이터 프레임을 월드와 평행($$\mathbf{R}^W_E = \mathbf{I}$$)으로 잡으면 수송운동은 순수 수직 병진으로 환원된다.

$$\mathbf{a}^W_I = [0,\ 0,\ a^W_{Ez}]^\top + \mathbf{a}^E_I$$

최종 상태는 18차원이다. $$\mathbf{x}_{rel} = [\mathbf{p}^E_I, \mathbf{q}^E_I, \mathbf{v}^E_I, \mathbf{b}_a, \mathbf{b}_\omega]$$와 $$\mathbf{x}_{elev} = [p^W_{Ez}, v^W_{Ez}, a^W_{Ez}]$$(엘리베이터의 수직 변위, 속도, 가속도)다.

직관적으로는 "로봇이 어떤 방 안에서 어떻게 움직이는가"(상대)와 "그 방 자체가 위아래로 어떻게 실려 가는가"(수송)를 따로 장부에 적는 것이다. 엘리베이터 가속도 $$a^W_{Ez}$$만 명시적 latent 상태로 두는 이유는, 수송 가속도는 비교적 매끄럽게 변해 random walk로 모델링하기 좋은 반면 로봇 상대 가속도는 변동이 커서 LiDAR 업데이트로 간접 보정하는 편이 낫기 때문이다. jerk 같은 고차항은 차원과 노이즈만 키워 제외했다. 중력은 정적 초기화 후 고정한다. 엘리베이터 프레임에서는 중력이 bias, 수송가속도와 강하게 coupling되어 온라인 추정 시 수치가 불안정하다.

### Mode-dependent IESKF

같은 augmented 상태를 항상 들고 다니되, 모드 매니저가 in/out을 판정한다. 비관성 모드에서는 엘리베이터 coupling 블록과 수송상태 블록을 전이행렬 $$\mathbf{F}_x$$와 노이즈 $$\mathbf{F}_w$$에서 활성화한다. 관성(일반) 모드에서는 그 블록들을 비활성화해 추정기가 자동으로 표준 FAST-LIO2식 LIO로 degenerate한다. 일반 실내에서는 평범한 LIO처럼 동작하고 엘리베이터에서만 추가 항이 켜지는 것이다.

LiDAR 업데이트는 엘리베이터-로컬 프레임 $$F_E$$에서 point-to-plane 잔차로 수행한다. 잔차의 야코비안이 수송상태 $$\delta\mathbf{x}_E$$에 대해 0이며, 이것이 "닫힌 캐빈에서 수송 상태가 약관측"이라는 사실의 수학적 근원이다. 수송 상태는 얼지 않고 IMU 프로세스 모델로 계속 propagate되지만 공분산만 자라기 때문에 출구 보정이 필수다. 월드 odometry는 로컬 상대위치를 엘리베이터 높이로 들어올려 합성한다.

$$\mathbf{p}^W_I = \mathbf{p}^E_I + p^W_{Ez}\,\mathbf{e}_3$$

### 출구 보정 — Zero-State Update와 Re-anchoring

엘리베이터가 멈춘 순간은 물리적으로 정지 상태이므로 수송 속도와 가속도는 0이어야 한다. 이를 고신뢰 prior 관측으로 만들어 EKF식 1회 업데이트(관측 노이즈 $$\mathbf{R} = \mathrm{diag}(10^{-5}, 10^{-4})$$ 수준)로 $$v^W_{Ez}$$, $$a^W_{Ez}$$를 0으로 강하게 끌어내린다. 교차공분산 구조를 통해 이 보정이 누적 수직 드리프트를 끌어내리는 것이 핵심이다.

이어서 re-anchoring을 한다. 엘리베이터 변위를 상대위치에 흡수하고($$\mathbf{p}^E_I \leftarrow \mathbf{p}^E_I + p^W_{Ez}\,\mathbf{e}_3$$) 수송상태를 0으로 리셋한다. 글로벌 위치는 보존한 채 표준 LIO 좌표로 복귀하며, 비관성 단계에서 쌓인 불확실성이 이후 항법을 오염시키지 않도록 교차공분산 블록을 0으로, 대각은 작은 prior로 재설정한다.

비유하자면, 엘리베이터 안에서는 "내가 몇 층 올라왔는지"가 불확실하게 떠 있다가, 문이 열려 발이 단단한 바닥에 닿는 순간 "여기가 정확히 이 층"이라고 못을 박고 떠다니던 오차를 정리하는 것이다.

### Elevator Mode Manager — 이벤트 트리거

진입(Flag_Entry)은 공간 협소화 패턴을 이용한다. 유효 LiDAR 점의 94 퍼센타일 수평거리 $$d_{max}$$가 임계 $$d_{th} = 3.0$$m 미만으로 $$\Delta t_{door} = 2.0$$s 유지되면 "문 닫힘 = 진입"으로 선언한다. 퍼센타일과 지속시간 조건으로 노이즈와 동적 occlusion에 대한 강건성을 확보한다.

퇴출(Flag_Exit)은 추정된 수직속도 $$v^W_{Ez}$$의 슬라이딩 윈도우 분산 패턴(가속과 감속 두 피크 사이의 정속 저분산 구간)을 FSM으로 추적해 정지 확정 시 트리거한다. 외부 인프라(건물 IoT)가 필요 없고 온보드 센싱만 쓴다. 인터페이스는 Flag만 노출하는 모듈식이라 비전이나 멀티모달 검출기로 교체할 수 있다.

### Geometry-aware Adaptive Downsampling

현재 프레임 유효점 수 $$N_t$$가 목표 $$N_{target}$$에 가깝도록 voxel 크기를 비례 피드백으로 온라인 조절한다.

$$v_{t+1} = v_t \left(\frac{N_t}{N_{target}}\right)^{1/\alpha}, \quad \alpha = 1.2$$

$$\alpha$$는 점수와 해상도의 비선형 관계를 반영한 유효 차원이며, voxel 크기는 $$[0.05, 0.8]$$m로 클램프한다. 넓은 곳에서는 voxel을 키워 연산을 절약하고, 협소한 캐빈에서는 줄여 기하 디테일을 보존한다.

### 매핑

ikd-Tree(FAST-LIO2 계열) 글로벌 맵을 쓴다. 로컬 등록은 $$F_E$$에서, 글로벌 삽입은 엘리베이터 높이로 들어올려 $$F_W$$로 투영한다.

## 실험과 결과

시스템은 Pre-processing(버퍼, 정적 초기화, adaptive downsampling), State estimation + Elevator mode management, Mapping으로 구성된다. C++로 구현되었고 ROS1/ROS2를 모두 지원하며, Livox custom과 표준 ROS 포인트클라우드를 받는다. 센서는 단일 Livox Mid360(통합 IMU)만 쓰고, Jetson Orin Nano와 시각 컨텍스트 기록용 산업용 카메라로 핸드헬드 플랫폼을 구성했다.

데이터는 자체 수집 실세계 데이터셋 4개 카테고리(Office/Dormitory/Campus/Mall), 총 20개 시퀀스, 79회 엘리베이터 탑승이다. 거울 반사, 보행자, 장거리 수직이동 등 난조건을 포함한다. 공개 벤치마크는 Hilti 2022/2023이고, 추가로 자체 시뮬레이터를 썼다. 베이스라인은 Point-LIO, FAST-LIO2, VoxelMap, LIO-SAM이며, Mid360 포맷과 6축 IMU를 지원하지 않는 방법은 변환 노드나 커뮤니티판으로 보정해 공정 비교했다. 연산 환경은 데스크톱 i5-14600KF/32GB이고, 실시간성은 Jetson Orin Nano 4GB에서도 검증했다.

실세계는 완전한 GT가 없어 종단 수직오차(terminal vertical error, $$e_z = z_{end} - z_{ref}$$)와 같은 층 재방문 및 계단-엘리베이터 교차검증을 지표로 쓴다. 표준 환경(Hilti)은 GT 대비 ATE RMSE다.

핵심 결과 세 가지는 다음과 같다.

1. 엘리베이터 시나리오 전승(20/20 성공). 베이스라인 4종은 전 시퀀스에서 실패했다. Point-LIO는 20/20 Type-I 드리프트(LiDAR prior 의존이 강해 엘리베이터 수직운동을 흡수하지 못하고 서로 다른 층 점군을 같은 맵에 겹침), VoxelMap과 LIO-SAM은 20/20 Type-II 발산, FAST-LIO2는 11 드리프트/9 발산이다. Elevator-LIO는 전부 성공했다.
2. 종단 수직오차 1cm 미만(17/20 시퀀스). Full 시스템에서 대부분 mm~cm급이다. Dormitory4는 누적 수직이동 138.9m 왕복 후에도 시작 높이로 거의 복귀했다.
3. 표준 환경 경쟁력 유지(Hilti 2022/2023). 비관성 기능을 넣어도 일반 LIO 성능을 해치지 않는다. 여러 시퀀스에서 best/second-best이며, Hilti2023 Underground1은 0.013m로 최고다. 다만 Stair, Long Corridor 등 일부에서는 1m급으로 열위라 균일 최적은 아니다.

| 방법 | Type-I 드리프트 | Type-II 발산 | 성공 |
|---|---|---|---|
| Point-LIO | 20 | 0 | 0 |
| FAST-LIO2 | 11 | 9 | 0 |
| VoxelMap | 0 | 20 | 0 |
| LIO-SAM | 0 | 20 | 0 |
| Elevator-LIO | 0 | 0 | 20 |

ablation에서 ZUPT(zero-state update)가 결정적이다. No ZUPT 변형은 수직오차가 미터급까지 치솟는다(Office4 -3.074m, Dormitory2 -2.31m). Full에서는 대부분 mm다. Map Reset 변형(층 도착마다 ikd-Tree를 비우고 재구축)은 과거 맵 재방문 보정을 제거해 "순수한 비관성 추정 능력"을 더 잘 반영하는데, 저자는 Full의 mm급 오차를 "로컬 비관성 추정 + 과거 맵 정렬의 결합 결과"로 정직하게 해석하며 전 구간 sub-cm 절대 높이 정확도는 아니라고 명시한다.

시뮬레이션 robustness 실험에서는 초기 중력 정렬오차, 캐빈 경사, 시변 중력 드리프트를 small/medium/large로 주입해도 return-to-origin 높이오차가 유계였다(최악 combined-large 0.312m). 정지 이벤트의 물리 구속이 모델 불일치를 끌어당긴다는 뒷받침이다. 검출기 한계도 실측되었다. Campus4와 Mall1의 거울 캐빈에서 다중경로 의사구조로 거리 임계 진입 검출이 실패해, 해당 시퀀스는 수동 트리거로 코어 추정기와 분리 평가했다.

기여 주장을 실제와 대조하면, 엘리베이터 비관성 프레임 분리 운동방정식 유도는 타당하나 캐빈 회전과 횡운동 무시 등 단순화 가정에 의존한다. ZUPT와 경량 검출기로 드리프트를 억제한다는 주장은 ablation으로 강하게 입증되어 핵심 기여다. feedback adaptive downsampling은 합리적이나 그 자체의 정량 ablation은 약하고 AD-LIO 등 선행과의 차별성은 제한적이다. 벤치마크 자원 공개는 약속 단계로 현재 미공개다. 비교 베이스라인이 "엘리베이터 미대응 기성 LIO"라 전승 대 전패는 다소 예견된 대결이지만, Hilti 경쟁력 유지, 시뮬 robustness, 정직한 오차 해석으로 설득력을 확보했다.

## 결론과 의의

"움직이는 비관성 캐리어 안에서의 연속 LIO"라는 거의 미개척 문제를, FAST-LIO2 골격에 최소 침습적으로(상태 augment + 모드 스위칭 + 이벤트 트리거 ZUPT) 풀어낸 첫 체계적 시도다. 일반 실내에서는 표준 LIO로 자동 환원되는 설계가 실용적이다. 저자도 명시하듯 지하철, 버스, 선박, 항공기 같은 일반 이동 베이스로 일반화할 수 있는 프레임이며, 멀티플로어 서비스 로봇(배달, 청소, 물류)의 실전 실패점인 엘리베이터를 직접 겨냥한다.

로봇 실무 관점에서 정리하면 다음과 같다.

- 어디에 쓸 수 있는가: 실내 다층 건물 모바일 로봇 자율주행에서 엘리베이터 탑승 구간의 localization 연속성 확보에 직접 적용된다. 단일 Livox Mid360과 Jetson Orin Nano 4GB로 실시간 동작이 검증되어 엣지 친화적이며, ikd-Tree/IESKF 기반이라 FAST-LIO2 수준의 가벼운 부하다.
- 재구현 체크포인트: 코드가 미공개(출판 후 공개 예정, 라이선스 미정)이므로 당장은 재구현이 필요하다. 알고리즘 식이 비교적 완결적이어서 FAST-LIO2 베이스에서 재구현 자체는 가능하다. 18차원 augmented 상태와 모드별 $$\mathbf{F}_x$$/$$\mathbf{F}_w$$ on/off, $$F_E$$ 로컬 point-to-plane 잔차, 출구 ZUPT + re-anchoring + 공분산 리셋, 94 퍼센타일 진입 검출과 속도 분산 FSM 퇴출 검출, adaptive downsampling이 핵심이다. 엘리베이터 데이터와 트리거 임계 튜닝이 필요하므로 재현 난이도는 중상이다.
- 한계 1, 검출기 일반성: 거리 임계 기반 진입 검출이 완전 거울, 투명, 대형 화물 엘리베이터에서 실패한다. 비전 시맨틱, 건물 IoT, 평면도 prior 융합이 필요하다.
- 한계 2, 모델 단순화와 약관측: 엘리베이터 수직 병진만 모델링하고 회전과 횡 외란은 무시하므로 요동치는 배나 버스로 가면 부족하다. 캐빈 안에서 절대 수직위치는 여전히 약관측이며, mm급 종단오차는 과거 맵 정렬 덕이라고 저자 스스로 인정한다.
- 한계 3, 전역 일관성과 검증: 루프클로저와 팩터그래프가 없어 장기 층간 맵 일관성은 향후 과제다. 움직이는 엘리베이터 내 6-DoF 고정밀 GT를 얻지 못해 절대 정확도 검증은 제한적이다.

## 참고

- 논문: [arXiv:2605.24495](https://arxiv.org/abs/2605.24495)
- 프로젝트 페이지: [Elevator_LIO_Page](https://xiaofan4122.github.io/Elevator_LIO_Page/) (코드 미공개)
- FAST-LIO2: Fast Direct LiDAR-Inertial Odometry (T-RO 2022) — IESKF + ikd-Tree 골격의 출처
- Point-LIO (Advanced Intelligent Systems 2023) — 엘리베이터 실험 주 비교군
- Zero-velocity detection / ZUPT (Skog et al., TBME 2010) — 출구 zero-state update의 이론적 뿌리
- VoxelMap (RA-L 2022) — 비교군이자 adaptive voxel 선행
- AD-LIO (IoT-J 2026) — geometry-aware adaptive downsampling의 직접 선행
