---
layout: post
title: "Principles and Guidelines for Evaluating Social Robot Navigation Algorithms (ACM THRI 2025)"
date: 2026-06-27 09:00:00 +0900
description: 31인 협업이 소셜 로봇 내비게이션의 정의와 8대 원칙, 지표·시나리오·벤치마크·데이터셋·시뮬레이터 가이드라인, 공통 지표 API를 제안한 합의 성격의 평가 표준 논문
tags: [social-navigation, navigation]
categories: paper-review
thumbnail:
giscus_comments: false
related_posts: false
toc:
  sidebar: left
---

| 항목 | 내용 |
|---|---|
| 저자 | Anthony Francis, Claudia Pérez-D'Arpino, Chengshu Li, Fei Xia, Alexandre Alahi, Rachid Alami, Aniket Bera, Abhijat Biswas, Joydeep Biswas, Rohan Chandra, Hao-Tien Lewis Chiang, Michael Everett, Sehoon Ha, Justin Hart, Jonathan P. How, Haresh Karnan, Tsang-Wei Edward Lee, Luis J. Manso, Reuth Mirsky, Sören Pirk, Phani Teja Singamaneni, Peter Stone, Ada V. Taylor, Peter Trautman, Nathan Tsoi, Marynel Vázquez, Xuesu Xiao, Peng Xu, Naoki Yokoyama, Alexander Toshev, Roberto Martín-Martín (Google, Stanford, UT Austin 등 20여 기관) |
| 발표 | ACM Transactions on Human-Robot Interaction (THRI) Vol.14 No.2, Art.34, 2025-02 (arXiv 2306.16740, 2023-06 제출, v4 2023-09) |
| 링크 | [arXiv](https://arxiv.org/abs/2306.16740) · [Code](https://github.com/SocialNav/SocialNavAPI) |

## 한 줄 요약

이 논문은 새 알고리즘을 제안하지 않는다. 사람이 공존하는 공간에서 로봇을 주행시키는 소셜 로봇 내비게이션을 공정하고 재현 가능하게 평가하기 위해, 31인의 대형 협업이 (a) "사회적으로 주행하는 로봇"의 정의와 8대 원칙(안전·편안함·가독성·예의·사회규범·타에이전트 이해·능동성·맥락적 적절성), (b) 지표·시나리오·벤치마크·데이터셋·시뮬레이터에 대한 가이드라인 묶음, (c) 시뮬레이터 간 결과 비교를 위한 공통 지표 API를 제시한 합의(consensus) 성격의 survey/position 논문이다. 요컨대 "분야의 평가 표준을 어떻게 세울 것인가"를 다룬 문헌이다.

## 연구 계보와 동향

소셜 내비게이션은 역사가 길지만 "무엇이 social인가"에 대한 합의가 없었고, 평가 방식이 연구실마다 제각각이었다. 컴퓨터비전(ImageNet)과 NLP(GLUE류)가 공통 벤치마크로 폭발적으로 발전한 것과 대조적으로, 소셜 내비는 100개에 달하는 서로 다른 지표, 호환되지 않는 시뮬레이터, 비교 불가능한 데이터셋 때문에 "어느 방법이 더 나은가"를 말하기 어려웠다.

핵심 선행 흐름은 다음과 같다.

- Kruse et al. (2013) "Human-Aware Robot Navigation: A Survey": comfort·naturalness·sociability를 정의한 초기 서베이. 이 논문이 8원칙으로 확장하는 출발점.
- Rios-Martinez et al. (2015): proxemics를 socially-aware navigation에 연결한 서베이. 개인공간(intimate 0.45m / personal 1.2m) 개념의 출처.
- Gao & Huang (2021) "Evaluation of Socially-Aware Robot Navigation": 177편을 리뷰해 지표·시나리오·데이터셋을 집계. 이 논문이 직접 토대로 삼는 평가 중심 서베이.
- Mavrogiannis et al. (2021) "Core Challenges of Social Robot Navigation": 내비 알고리즘·인간행동모델·평가의 핵심 난제 정리.
- Mirsky et al. (2021): conflict(개입 없으면 충돌할 단기 조우) 개념. P6·P7의 근거.

직접적 계기는 2022 Social Navigation Symposium이다. 다양한 정의와 방법이 충돌하던 토론을 정리해 합의 taxonomy로 수렴시킨 결과물이 이 논문이다. 개별 알고리즘 논문이 아니라 분야 차원의 "평가 헌법"을 제안한 position 논문으로, (1) social의 모호함을 8원칙으로 operationalize하고, (2) 평가 도구를 하나의 공통 taxonomy와 시나리오 카드·지표 3글자 코드로 표준화하며, (3) 실제 코드(SocialNavAPI)로 통합 지표 API의 첫발을 뗀 점이 새롭다.

이후 "Characterizing the Complexity of Social Robot Navigation Scenarios"(2024), "How Do Robot Experts Measure the Success of Social Robot Navigation?"(HRI 2024), 그리고 다수 VLM 기반 소셜 내비 연구(AutoSpatial, Narrate2Nav 등)가 이 가이드라인을 평가 프레임으로 채택하고 있다. 본문이 리뷰한 도구들도 활발히 발전 중이다: HuNavSim(ROS2 SFM + Behavior Tree, RA-L 2023, 후속 2.0 2025), Arena-Rosnav 2.0, SocialGym 2.0, SEAN 2.0, SocNavBench, iGibson Challenge.

## 문제와 동기

로봇을 인간 환경에 폭넓게 배치하려면 사람 사이를 주행하는 능력이 필수인데, 그 평가가 어렵다. 정적 환경의 전통 내비와 달리 (1) 동적 인간 에이전트와 (2) 로봇 행동의 적절성에 대한 인간의 주관적 지각이 얽히기 때문이다. CV/NLP를 가속한 명확·재현·접근 가능한 벤치마크가 소셜 내비에는 없어, 알고리즘을 공정하게 비교하거나 한계를 드러내거나 유망한 방향을 가리킬 수 없었다. 대상 독자는 소셜 내비 정책을 개발·비교하려는 연구자, 벤치마크·시뮬레이터·데이터셋 제작자, 실로봇 배치를 준비하는 산업계다.

## 핵심 아이디어와 방법

핵심 정의는 "사회적으로 주행하는 로봇 = 자신의 내비 목표를 달성하되, 주변 에이전트의 경험이 저하되지 않거나 오히려 향상되도록 행동을 수정하는 로봇"이다. 이를 8원칙·평가 가이드라인·공통 API로 operationalize한다.

### 8대 원칙 (P1~P8)

최적화 관점에서 P1~P7은 주 목표(목적지 도달)에 더해 최적화할 추가 목적함수이고, P8(맥락)은 시점마다 어떤 원칙을 더 중요하게 가중할지 결정하는 메타 요소다. 원칙들은 완전히 직교하지 않는다(가독성이 높아지면 안전·편안함도 올라가는 식).

| 원칙 | 내용 |
|---|---|
| P1 안전(Safety) | 인간·다른 로봇·환경에 해를 끼치지 않음. 충돌 회피가 핵심이나 벽·기물 손상, 타 로봇 유발 사고도 포함 |
| P2 편안함(Comfort) | 짜증·스트레스를 유발하지 않음. proxemics(intimate 0.45m, personal 1.2m) 준수, 급정거·jitter·과속 회피 |
| P3 가독성(Legibility) | 행동만으로 목표를 추론할 수 있게 함(Dragan의 legibility, predictability와 구분). 통신 가능 로봇은 의도를 명시 가능 |
| P4 예의(Politeness) | 물리적 예의(끼어들지 않기) + 소통적 예의("on your left") |
| P5 사회규범(Social Competency) | 우측통행 등 관습 준수. 최적화 문제가 아니라 합의 따르기 |
| P6 타에이전트 이해(Agent Understanding) | 다른 에이전트의 의도·행동을 예측·수용. 대화 중인 두 사람 사이를 지날지 판단 등 |
| P7 능동성(Proactivity) | 교착(4거리 데드락, 좁은 문 경합) 해소를 위해 먼저 양보·제안. 너무 보수적이면 freezing robot 문제 |
| P8 맥락적 적절성(Contextual Appropriateness) | 문화·다양성·환경(기하/운영)·과업·대인 맥락에 따라 위 원칙 가중이 달라짐. 예: 병원 응급 카트는 예의보다 과업 성공이 우선 |

### 연구 방법론과 라이프사이클

연구 유형을 현장연구(in-the-wild) → 로봇 배치 → 실험실 실험 → 소셜 내비 시나리오 → 연출된 사회적 상호작용으로 구분하고, 이들을 하나의 라이프사이클(데이터 수집 → 이슈 발견 → 실험실 실험 → 시나리오 개발 → 벤치마킹 → 챌린지)로 묶는다. "야생 데이터"와 "통제 시나리오"를 대립이 아니라 순환으로 본다. 실세계 연구 가이드라인은 R1(안전 보존), R2(피험자 존중/IRB), R3(명확한 과학적 목표)다.

### 공통 taxonomy

시나리오·벤치마크·데이터셋·시뮬레이터가 공유하는 일반 요인(맥락·물리환경·인간 유저 유형·인간 행동·로봇 과업/역할·시나리오·커버리지·HW 플랫폼·센서/액추에이터·통신·수집 데이터·지표·행동 저작 방식·sim vs real)과, 도구별 고유 요인(데이터셋: 궤적 수·주행거리·조우 수·시점·주석·프라이버시 / 벤치마크: 플랫폼·데이터셋·베이스라인·리더보드·다운로드성 / 시뮬레이터: 추상화 수준·씬/에이전트 표현·물리/로봇/보행자 충실도)으로 정리한다.

### 지표 분류 (3글자 코드)

모든 지표를 세 축으로 분류해 3글자 코드를 부여한다. (1) 변수 유형: Social / Non-social / All-encompassing, (2) 본성: Hand-crafted / Sensor / Questionnaire / Learned 등, (3) 시간 범위: Step-wise / Task-wise. 예를 들어 Success Rate = NHT, 순간 표정 반응 = SSS, 전반 품질 설문 = AQT다.

권장 hand-crafted 지표(Table I)는 두 계열이다.

- 성공 계열(전통 내비 성능에서 퇴보하지 않도록): Success/SR, Collision/CR(벽·에이전트·인간 구분), Timeout, Failure to Progress, Stalled Time, Time to goal, Path Length, SPL.
- 품질·사회 계열: 속도/가속/jerk(min/avg/max), Clearing Distance, Space Compliance(임계 0.5m면 Personal Space Compliance), Minimum Distance to Human, Minimum Time-to-Collision, Aggregated Time.

권고 흐름은 이렇다. 설문(surveyed) 지표가 gold standard지만 비싸고 비재현적이므로, hand-crafted 공통 부분집합으로 시뮬에서 1차 검증(M2)한 뒤 검증된 설문(M5)으로 사회성을 평가하고, 항상 분포 정보(히스토그램)를 함께 보고한다(M8). 지표 가이드라인은 M1~M8(표준 지표 보고, 알고리즘 지표로 우선 검증, 맥락에 맞게 파라미터화, 학습 지표로 수용 테스트, 검증된 설문 사용, 편향 제거, 반복 분석, 심층 보고)이다.

### 시나리오 카드와 벤치마크·시뮬레이터 가이드라인

ML의 "model card"를 본떠 Social Navigation Scenario Card(메타데이터 / 정의 / 사용 가이드)를 제안한다. 공통 시나리오 목록(Table III)에는 FRONTAL APPROACH, OVERTAKING, INTERSECTION, BLIND CORNER, NARROW DOORWAY, ENTERING/EXITING ELEVATOR, JOINING/LEAVING GROUP, FOLLOWING/LEADING, CROWD/PARALLEL/PERPENDICULAR TRAFFIC, OBJECT HANDOVER, CRASH CART 등이 있다. 시나리오 가이드라인 N1~N8, 벤치마크 가이드라인 B1~B6(사회 행동 평가·정량 지표·베이스라인·효율/재현/확장·인간 데이터 근거·검증된 도구), 데이터셋 가이드라인 D1~D8, 시뮬레이터 가이드라인 S1~S9(표준 API·표준 지표·확장성·데이터셋/벤치마크/라벨링 지원·공통 모폴로지·상세 보행자·행동 저작)를 제시한다.

### 통합 지표 API

시뮬레이터마다 다른 출력을 하나로 모으는 공통 high-level metrics API다. 입력 명세(보행자/로봇/장애물 데이터)를 ROS message + OpenAI Gym info + JSON Schema로 정의하고, C++/Python 표준 라이브러리로 Table I 지표를 계산해 단일 출력 포맷으로 낸다. 각 시뮬레이터·로봇·데이터셋은 bridge code만 작성하면 된다.

비유하자면 CV에 ImageNet, NLP에 GLUE가 있었듯 "소셜 내비에도 공통 채점표를 만들자"는 시도다. 다만 채점 대상이 사람의 주관적 편안함이라 단일 점수로 환원이 안 되므로, 8개 채점 항목(원칙)과 항목별 채점 규칙(지표/시나리오/벤치마크 가이드), 그리고 시뮬레이터마다 다른 답안지를 같은 양식으로 변환하는 어댑터(API)를 동시에 제시한 셈이다.

## 실험과 결과

이 논문은 새 알고리즘을 학습·평가하지 않는다. 대신 기존 도구들의 체계적 메타 분석 표가 결과물이다.

- **벤치마크 9종 비교(Table IV):** ArenaBench, CrowdBot, DynaBarn, gym-collision-avoidance, HuNavSim, iGibson, SocNavBench, SEANavBench, Social Navigation Protocol을 분류·맥락·물리환경·유저 유형·시나리오·코너케이스 커버리지·시뮬 플랫폼·데이터셋·인간 행동 저작·충실도·지원 로봇·통신·리더보드·최종 업데이트, 그리고 B1~B6 충족도까지 격자로 평가한다.
- **데이터셋 16종(Table V):** JRDB, THOR, SCAND, MuSoHu, LCAS, ETH/UCY, TrajNet++, EIFPD, SDD, EFL, WILDTRACK, CrowdBot, DynaBarn, SocNav1/2, SACSoN, SG-LSTM를 맥락·환경·수집량·시나리오·플랫폼·센서·과업·지표로 정리한다.
- **시뮬레이터 13종(Table VI):** CrowdBot, CrowdNav, DynaBarn, gym-collision-avoidance, HuNavSim, iGibson, InHuS, IMHuS, Menge-ROS, PedSim-ROS, SEAN 2.0, SocialGym 2.0, SocNavBench를 포커스·플랫폼·에이전트/씬 표현·물리/로봇/보행자 충실도·보행자 반응(SFM·ORCA·replay)·상호운용(Gym·ROS)으로 비교한다.
- 규모 일화로 캠퍼스 실내 1,000km 주행, Google 배치 3,000km 이상이 인용되며, 실로봇 시행은 표본 30회 이상을 경험칙으로 권장한다.

가장 중요한 산출물 세 가지를 꼽으면 (1) 8대 원칙 + 통합 taxonomy, (2) 실행 가능한 가이드라인 코드 묶음(P/R/M/N/B/D/S) + Scenario Card + 지표 3글자 분류·권장 지표 표, (3) 벤치마크 9 / 데이터셋 16 / 시뮬레이터 13종의 메타 비교 표 + 통합 지표 API다. 정량 SOTA 경쟁이 아니라 "무엇을 어떻게 측정해야 하는가"의 합의가 결과다.

**읽을 때 주의할 점.** 8원칙 정의는 심포지엄 합의 기반으로 잘 정리됐으나 "경험이 저하되지 않거나 향상"을 측정 가능하게 조작화하는 문제는 여전히 모호하다(저자도 인정). 가이드라인 대부분은 "should/recommend" 수준이라 강제력·정량 임계값이 거의 없다(예: Space Compliance 임계는 맥락별로 보고하라고만 한다). 가장 약한 주장은 통합 API다. 확인 시점 기준 `SocialNav/SocialNavAPI` repo는 커밋 1개·파일 1개(main_api.py), README와 license가 없었고, C++/Python 라이브러리·JSON Schema·SEAN/SocialGym/DynaBarn bridge는 "개발 중"으로 서술되어 실제 사용 가능한 구현 증거를 확인하지 못했다. 설계 청사진은 명확하나 채택·검증은 미입증이다. 한편 본문은 "한 지표가 다른 지표보다 낫다고 정량적으로 결론낼 방법을 찾지 못했다", "학습 지표는 아직 채택 준비가 안 됐다", "시뮬 보행자 반응(replay vs SFM/ORCA)은 둘 다 불완전하다"고 스스로 명시해 과장이 적고 한계 인정이 강점이다.

## 결론과 의의

소셜 로봇 내비게이션은 전통 내비의 모든 문제에 "로봇이 움직이는 인간과 어떻게 함께할 것인가"와 "인간이 그 참여에 어떻게 반응하는가"라는 두 난제를 더한다. 이 논문은 그 평가를 위한 공유 언어와 합의 프레임(8원칙 + taxonomy + 가이드라인 + Scenario Card + 공통 API)을 제시해, 파편화된 평가를 비교 가능하게 만드는 토대를 놓았다. 핵심 메시지는 "좋은 소셜 내비 정책을 만드는 것만큼, 그것을 공정·재현 가능하게 측정하는 인프라가 중요하다"이다.

로봇 실무 관점에서 정리하면 다음과 같다.

- **바로 쓸 수 있는 것.** 실내 다층 건물 모바일 로봇 자율주행에서 사람과의 조우를 평가할 때, Table I의 hand-crafted 지표(SR·충돌·SPL·Space Compliance·최소 거리·TTC 등)와 Scenario Card 양식은 큰 비용 없이 채택할 수 있다. 특히 NARROW DOORWAY, ENTERING/EXITING ELEVATOR, BLIND CORNER 같은 시나리오 정의는 실내 로봇의 회귀 테스트 항목으로 그대로 옮길 만하다.
- **시뮬레이션 벤치마킹.** 본문이 리뷰한 HuNavSim(ROS2 + Gazebo), Arena-Rosnav 2.0, SocialGym 2.0, SEAN 2.0, SocNavBench, iGibson은 실제로 다운로드 가능하고 활발하다. 이들로 시나리오를 셋업해 정책을 벤치마킹하는 것이 중간 난도의 적용 경로다. 데이터셋(ETH/UCY, SCAND, JRDB, THOR, SDD, MuSoHu, SACSoN, SocNav1/2 등)도 접근성이 좋다.
- **막히는 부분.** 통합 SocialNavAPI를 직접 쓰려면 구현이 미성숙해 bridge code를 사실상 새로 짜야 한다. 검증된 인간 설문(M5/M6)은 IRB·test-retest·construct validity·Cronbach α 반복 검증이 필요해 노동집약적이고 고비용이다. all-encompassing 학습 지표는 "채택 준비 안 됨" 상태다.
- **구조적 한계.** 시뮬 보행자는 replay(반응 없음)든 SFM/ORCA(단순 모델)든 불완전해, 시뮬에서 잘 되는 정책이 실세계 인간 반응으로 전이된다는 보장이 약하다(sim-to-real 간극). 사람이 로봇에 익숙해지며 지표가 시간에 따라 변하는 문제(신기성 효과, robot wrangler 편향, STEM 피험자 편향)와 문화·다양성 맥락은 열려 있다. 음성·디스플레이로 의도를 알리는 로봇이 표준 지표에서는 오히려 덜 사회적으로 보이는 불공정도 남는다.

## 참고

- 논문: [arXiv 2306.16740](https://arxiv.org/abs/2306.16740) (ACM THRI Vol.14 No.2, 2025)
- 코드: [SocialNav/SocialNavAPI](https://github.com/SocialNav/SocialNavAPI) (참조 구현, 초기 단계)
- Human-Aware Robot Navigation: A Survey (Kruse et al., 2013)
- Evaluation of Socially-Aware Robot Navigation (Gao & Huang, 2021)
- Core Challenges of Social Robot Navigation: A Survey (Mavrogiannis et al., 2021)
- Legibility and Predictability of Robot Motion (Dragan et al., 2013)
- HuNavSim: A ROS 2 Human Navigation Simulator (Pérez-Higueras et al., RA-L 2023)
