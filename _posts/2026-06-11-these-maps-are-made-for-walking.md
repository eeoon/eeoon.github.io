---
layout: post
title: "These Maps Are Made For Walking: Real-Time Terrain Property Estimation for Mobile Robots (RA-L 2022)"
date: 2026-06-11 09:00:00 +0900
description: 단일 RGB-D로 지형 높이와 마찰계수 확률분포를 베이지안 재귀 추정하는 실시간 시맨틱 맵핑 프레임워크
tags: [terrain, legged, mapping, traversability]
categories: paper-review
thumbnail:
giscus_comments: false
related_posts: false
toc:
  sidebar: left
---

| 항목 | 내용 |
|---|---|
| 저자 | Parker Ewen, Adam Li, Yuxin Chen, Steven Hong, Ram Vasudevan (University of Michigan, Robotics Institute) |
| 발표 | IEEE Robotics and Automation Letters (RA-L) vol. 7 no. 3, 2022 (IROS 2022 발표) |
| 링크 | [arXiv](https://arxiv.org/abs/2205.12925) · [Code](https://github.com/roahmlab/sel_map) · [Dataset](https://github.com/roahmlab/terrain_friction_dataset) |

## 한 줄 요약

단일 RGB-D 카메라로부터 지형의 높이(elevation)와 마찰계수의 확률분포를 베이지안 추론으로 재귀적으로 동시 추정하는 실시간 시맨틱 맵핑 프레임워크다. 시맨틱 분할망이 내는 클래스 확률을 Dirichlet 켤레사전분포로 누적하고, 클래스별로 직접 측정한 마찰 분포를 가중 혼합해 각 지형 타일의 마찰을 다봉 가우시안 분포로 출력한다. "통과 가능/불가능"이라는 이진 traversability 라벨을 넘어, 다리 로봇이 미끄러움을 정량적으로 알고 보행을 조정할 수 있게 하는 것이 목표다.

## 연구 계보와 동향

논문은 세 갈래의 선행 연구를 합치는 위치에 있다.

- **기하 맵핑:** Fankhauser 등의 확률적 지형 elevation map(2018)이 표준 출발점이다. 2.5D 삼각 메쉬로 지형 표면을 표현하고 칼만 필터로 센서·포즈 노이즈를 흡수한다. 이 논문의 elevation map 모듈은 이 계보를 그대로 잇는다. 단점은 기하만 알고 마찰 같은 물성을 모른다는 것이다.
- **시맨틱 맵핑:** 이미지나 점군에 신경망 분할을 적용해 지형 클래스를 기하 위에 투영한다. Brandão의 "Friction from vision"(2016), Noh의 SMDRA 데이터셋(2021) 등이 RGB로 마찰을 예측했다. 단점은 비재귀적이라 사전정보로 노이즈 추정을 정제하지 못한다는 것이다.
- **Traversability 추정:** 물성 추정을 우회해 "갈 수 있나"만 추정한다. 그러나 traversability는 로봇의 이동 방식·속도 같은 내부 상태에 의존해 과대·과소 근사가 생긴다(Kim 2006).

이 논문은 기하(elevation) + 물성(friction) + 재귀적 베이지안 갱신 + 확률분포 출력을 동시에 달성한다. 후속 흐름으로는 ETH Zurich의 Multi-Modal Elevation Mapping(2023)과 self-supervised traversability 학습 계열이 연결되지만, 후속 인용 관계는 부분만 확인되었다.

## 문제와 동기

모바일·다리 로봇의 운동방정식은 지형 물성(마찰계수, 접촉모델 파라미터)에 의존한다(Neunert 2018). "어디가 높다"만으로는 안정적 보행을 계획할 수 없고, "그 표면이 얼마나 미끄러운가"를 알아야 한다.

이상적인 물성 맵의 요구조건은 세 가지다. (1) 실시간 동작, (2) 센서 노이즈 완화, (3) 물성을 단일 값이 아닌 확률분포로 제공해 위험 회피 계획을 가능하게 할 것. 기존 방법은 비재귀적이거나, 점추정만 하거나, 물성 자체가 없어서 이 셋을 동시에 만족하지 못했다.

## 핵심 아이디어와 방법

### 표현: 확률 삼각 메쉬

지형을 2.5D 삼각 메쉬로 표현한다. 각 정점(vertex)은 $$[x, y, z, \sigma^2]$$ 로 높이와 그 분산을 가지며, 각 면(face)은 세 정점, 내부점 집합, 그리고 Dirichlet 파라미터 벡터 $$\alpha$$ 를 가진다. 비유하면 지형을 작은 삼각 타일로 깐 마룻바닥인데, 각 타일이 "내 높이는 이만큼 ± 오차이고, 나는 잔디일 확률 60%, 콘크리트 30%"라는 자기소개를 들고 있는 셈이다.

### 재귀적 elevation map: 1D 칼만 필터

RGB-D 깊이로 만든 점을 Barycentric 좌표로 삼각형 내부 판정해 해당 면에 배정한다. 정점 높이는 1차원 칼만 필터로 갱신한다. 측정 분산은 깊이 센서 노이즈 공분산 $$\Sigma_s$$ 와 카메라 포즈 공분산 $$\Sigma_p$$ 를 오차전파법칙으로 합쳐 계산한다.

$$
\sigma^2 = J_s \Sigma_s J_s^T + J_p \Sigma_p J_p^T
$$

같은 타일을 여러 번 볼수록 높이 추정이 점점 또렷해진다.

### 재귀적 지형 클래스 추정: Dirichlet-Categorical 켤레

핵심 수학적 트릭이다. 시맨틱 분할망은 픽셀마다 Categorical 분포(클래스 확률)를 낸다. 그 켤레사전분포인 Dirichlet 분포를 쓰면, 새 관측이 올 때마다 적분 없이 단순 덧셈으로 사후분포를 갱신할 수 있다.

$$
\tilde{\alpha}_j = \alpha_j + \sum_i \mathbb{1}\{z_i = j\}
$$

새 측정이 클래스 $$i$$ 일 확률은 $$f(z=i \mid Z, \alpha) = \tilde{\alpha}_i / \sum_j \tilde{\alpha}_j$$ 로 폐형식이다. 항아리에 색깔 구슬을 넣는 것과 같다. 잔디로 분류될 때마다 초록 구슬을 하나 더 넣고, 항아리의 비율이 곧 클래스 확률이 된다. 이 폐형식 갱신이 실시간성의 수학적 심장이다.

### 클래스에서 물성으로: 전확률 법칙과 가우시안 혼합

클래스별 마찰계수를 단일 값이 아니라 조건부 분포 $$f(\psi \mid z=i) = \mathcal{N}(\mu_i, \sigma_i^2)$$ 로 모델링하고, 전확률 법칙으로 결합한다.

$$
f(\psi \mid Z, \alpha) = \sum_i \frac{\alpha_i}{\sum_j \alpha_j} \cdot \mathcal{N}(\mu_i, \sigma_i^2)
$$

즉 클래스 확률로 가중된 다봉(multimodal) 가우시안 혼합이다. "이 타일은 70% 콘크리트($$\mu \approx 0.54$$) + 30% 얼음($$\mu \approx 0.19$$)"이면 마찰 분포가 두 봉우리로 나온다. 단일 점추정이 놓치는 불확실성을 보존한다.

### 마찰 데이터셋: 두 번째 기여

기존 데이터셋은 클래스당 약 3개 측정뿐이거나 비공개였다. 저자들은 로드셀로 견인력을 측정하는 장치를 제작해 $$\mu = F_{pull}/(mg)$$ 로 마찰계수를 구하고, 10개 지형 클래스에서 약 1만 샘플을 수집했다. 가우시안·Weibull·로그정규 중 Kolmogorov-Smirnov 검정으로 단봉 가우시안이 가장 잘 일반화됨을 확인해 채택했다. 대표 파라미터는 다음과 같다.

| 클래스 | $$\mu$$ | $$\sigma$$ |
|---|---|---|
| 고무 | 0.616 | 0.048 |
| 잔디 | 0.577 | 0.077 |
| 콘크리트 | 0.543 | 0.065 |
| 자갈 | 0.428 | 0.059 |
| 눈 | 0.390 | 0.071 |
| 라미네이트 바닥 | 0.311 | 0.045 |
| 얼음 | 0.192 | 0.046 |

## 실험과 결과

**구현:** C++와 ROS 인터페이스. Realsense RGB-D 노이즈 모델 내장. 로봇 주변 5m 반경을 2cm 해상도로 매핑하며 9±5Hz로 동작한다. 개발 하드웨어는 Ryzen 3600, 32GB RAM, RTX 2080 Ti다.

**시뮬레이션:** CARLA에서 지형별 ground-truth 마찰을 제공받아 평가했다. ADE20K 사전학습 분할망을 썼다. 비교군은 (1) Unimodal Non-Recursive(매 순간 최빈 클래스의 단봉 가우시안, 점추정 CNN류 대표)와 (2) Multimodal Non-Recursive(전체 Categorical 분포로 다봉 추정하되 재귀 갱신 없음)다.

**실로봇:** Boston Dynamics Spot에 온보드 Realsense D435를 달고 실내외 다양한 지형(빙판, 언덕, 저식생 등)에서 시연했으며, traversability 맵핑(Gan 2021)과 비교했다. Agility Robotics Digit에서도 동작한다고 언급된다.

핵심 결과 세 가지는 다음과 같다.

1. **KL divergence 2.4로 최저.** Unimodal Non-Recursive 42.3, Multimodal Non-Recursive 3.7 대비 ground-truth 분포에 가장 가깝다. 재귀 갱신이 분포 정확도를 끌어올린다.
2. **저마찰($$\mu \le 0.5$$) 영역에서 큰 폭 우위.** 고마찰 영역은 베이스라인과 비슷하지만, 안전에 결정적인 미끄러운 지형 식별에서 Precision-Recall 곡선상 확실히 앞선다. 평균 정밀도 0.99, 평균 정확도 0.95(Multimodal Non-Recursive 0.99/0.93)다.
3. **실시간 달성.** 1cm 메쉬 기준 총 527ms 중 분할망이 477ms, elevation·물성 갱신은 50ms에 불과하다. Fast-SCNN으로 분할망을 바꾸면 총 약 200ms로 준다. 메모리는 1m×1m 메쉬당 약 45~55MB다.

실로봇 정성 결과도 명확하다. 빙판에서 traversability 맵핑은 "안전"으로 오판하지만 이 방법은 낮은 마찰을 정확히 예측한다. 언덕과 저식생에서 traversability는 "통과 불가"로 과소 근사하지만 이 방법은 정상 추정한다. 다만 Table I에 없는 클래스가 추정되면 마찰 추정을 하지 않고 회색으로 칠한다.

기여 주장을 검증하면, 데이터셋 공개와 폐형식 베이지안 재귀 프레임워크는 실제로 부합한다. "9±5Hz 실시간"은 분할망 성능에 강하게 의존하므로 표현 그대로 보면 다소 낙관적이지만, 맵 갱신 자체(50ms)가 빠른 것은 사실이다.

## 결론과 의의

이 논문은 다리 로봇 맵핑을 "기하만"에서 "기하 + 물성 확률분포"로 끌어올렸다. 핵심 메시지는 미끄러운 지형을 피하는 것이 아니라, 미끄러움을 알고 보행을 조정하라는 것이다. 켤레사전분포로 실시간 재귀 갱신을 푼 수학적 설계는 그 자체로 재사용 가치가 높다.

로봇 실무 관점에서 정리하면 다음과 같다.

- **쓸 수 있는 곳:** risk-aware footstep planning의 입력으로 분포 형태의 마찰 맵이 필요한 다리 로봇, 또는 실외 비정형 지형 주행에서 시각 기반 물성 추정 계층이 필요한 경우. MIT 라이선스, 마찰 데이터셋, rosbag·CARLA 기반 검증 경로가 모두 공개되어 실로봇 없이도 시험할 수 있다.
- **재현 난이도는 중:** ROS Noetic, CUDA 11+, PyTorch, 외부 분할망 체크포인트 수동 배치 등 셋업 단계가 많다. NVIDIA GPU가 사실상 필수이며 CPU-only는 메모리 문제가 있을 수 있다.
- **마찰 측정의 대표성:** 견인식 로드셀 측정이 실제 발-지면 접촉 동역학(법선력·속도 의존, 충격)을 다 담지 못한다. 클래스당 평균 마찰이 실제 보행 안전과 얼마나 일치하는지는 열린 질문이다.
- **분할망 의존과 open-set 문제:** 정확도와 속도가 외부 분할 성능에 좌우되고, 학습된 클래스 밖의 신규 지형은 추정 불가다. 같은 "잔디"라도 젖음·마름에 따라 마찰이 다른데 정적 가우시안으로 고정되어 시간·기상 변화를 반영하지 못한다.
- **플래너 연동 미평가:** 출력 분포가 실제 footstep planning 성능을 얼마나 개선하는지의 end-to-end 검증은 범위 밖이다. LiDAR·다중 카메라 확장도 언급만 되고 미구현이다.

## 참고

- 논문: [arXiv 2205.12925](https://arxiv.org/abs/2205.12925) (DOI 10.1109/LRA.2022.3180439) · 코드: [github.com/roahmlab/sel_map](https://github.com/roahmlab/sel_map) · 데이터셋: [terrain_friction_dataset](https://github.com/roahmlab/terrain_friction_dataset)
- Fankhauser, Bloesch, Hutter, Probabilistic Terrain Mapping for Mobile Robots with Uncertain Localization (2018)
- Brandão, Hashimoto, Takanishi, Friction from Vision (2016)
- Noh et al., SMDRA: Surface Material Dataset for Robotics Applications (2021)
- Tu, The Dirichlet-Multinomial and Dirichlet-Categorical Models for Bayesian Inference (2014)
- Gan et al., Multi-task Learning for Scalable and Dense Multi-layer Bayesian Map Inference (2021)
