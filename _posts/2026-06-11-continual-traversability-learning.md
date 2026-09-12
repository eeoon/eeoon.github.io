---
layout: post
title: "Continual Learning for Traversability Prediction with Uncertainty-Aware Adaptation (RA-L 2025)"
date: 2026-06-11 09:00:00 +0900
description: 과거 데이터를 저장하지 않고 CVAE로 경험을 회상하되 불확실성으로 걸러 파국적 망각을 줄이는 traversability 연속학습
tags: [traversability, continual-learning, navigation, terrain]
categories: paper-review
thumbnail:
giscus_comments: false
related_posts: false
toc:
  sidebar: left
---

| 항목 | 내용 |
|---|---|
| 저자 | Hojin Lee, Yunho Lee, Daniel A. Duecker, Cheolhyeon Kwon (UNIST HMCL, TUM MIRMI) |
| 발표 | IEEE Robotics and Automation Letters (RA-L) vol. 10 no. 11, 2025 |
| 링크 | [DOI](https://doi.org/10.1109/LRA.2025.3619687) · [Code](https://github.com/HMCL-UNIST/Continual-Traversability-Learning) |

## 한 줄 요약

야지 주행 로봇이 아스팔트, 자갈, 모래, 숲처럼 서로 다른 지형을 순차적으로 학습할 때 새 지형을 배우면서 옛 지형을 잊어버리는 파국적 망각(catastrophic forgetting)을 다루는 연속학습 프레임워크다. 과거 데이터를 저장하는 대신 조건부 VAE로 가짜 과거 샘플을 생성해 재생(replay)하되, 그 샘플의 예측 불확실성을 척도로 신뢰할 만한 것만 골라 예측기와 생성기를 갱신한다. 메모리 비용은 환경 수와 무관하게 모델 파라미터 크기로 고정되며, 실로봇에서 다섯 환경을 학습한 뒤 첫 환경으로 돌아와도 전체 데이터를 다 보관한 상한 모델과 같은 항법 성능을 냈다.

## 연구 계보와 동향

논문은 "학습 기반 traversability 예측"(WayFAST, EVORA)과 "연속학습"(Learning without Forgetting, 연속학습 서베이)의 교집합에 위치한다. 야지 traversability에 생성형 재생(generative replay)을 도입한 것이 차별점이다.

직접 비교 대상이 되는 선행 연구는 다음과 같다.

- **WVN**(Frey 2023): 빠른 온라인 적응만 추구하고 과거 경험 보존은 무시해 망각이 발생한다.
- **IMOST**(Ma 2024): 점증형 동적 메모리 버퍼로 시각 특징을 군집화해 저장한다. 보존은 잘 되지만 환경 다양성에 비례해 메모리가 계속 늘어난다.
- **자기지도 온라인 연속학습**(Yoon 2024): 고정 크기 버퍼를 쓰므로 새 데이터를 넣을 때 유용한 정보를 폐기할 위험이 있다.

즉 replay 방식은 "확장성 대 표현력"의 트레이드오프를 안고 있다. 이 논문은 데이터를 저장하는 대신 CVAE 생성기로 과거를 합성하고, 생성형 연속학습의 고질적 약점인 "생성 샘플의 신뢰도를 고려하지 않음"을 불확실성 필터링으로 보완한다. 저자들의 선행 연구(3D 야지 불확실성 항법, terrain-aware kinodynamic MPPI)와 직결되며, 결론에서는 physics-informed foundation model(PIETRA 계열)을 향후 방향으로 언급한다.

## 문제와 동기

1. **일반화 실패:** 학습 기반 traversability 예측기는 학습 환경과 비슷한 조건에서는 잘 되지만 새 지형에서는 성능이 급락한다.
2. **적응의 부작용 = 망각:** 새 지형에 맞춰 모델을 갱신하면 새 경험이 과거 경험을 덮어써 이전 환경에서의 성능이 떨어진다.
3. **기존 해법의 한계:** 경험 재생은 과거 데이터를 메모리에 두어야 하므로 메모리가 폭증하거나(IMOST) 정보가 손실된다(고정 버퍼). 자원이 제한된 모바일 로봇에는 맞지 않는다.
4. **생성형 연속학습의 빈틈:** 생성 모델로 과거를 합성하면 저장은 불필요하지만, 합성 샘플이 실제 분포에서 벗어나면 오히려 모델을 망친다. 기존 생성형 연속학습은 이 불확실성을 무시했다.

## 핵심 아이디어와 방법

### 문제 설정: domain-incremental 연속학습

학습 과제(traversability 예측)는 고정이고 입력 분포(지형 특성)만 배포마다 바뀌는 domain-incremental 설정이다. traversability의 척도로 traction parameter $$\xi = [\xi^x, \xi^z]$$ 를 정의한다. 명령 속도 대비 실제 속도의 선형·각 괴리이며, unicycle 운동학에 곱해 skid-steering이나 다리 로봇을 모델링한다. 예측 모델 $$T$$ 는 지형 특징 $$\phi$$ 와 속도 $$v$$ 를 받아 traction의 가우시안 분포 $$\mathcal{N}(\mu, \sigma^2)$$ 를 출력한다.

### 자기지도 라벨링: EKF

로봇이 직접 굴러보면서 명령한 만큼 실제로 갔는지 측정한다. traction을 random-walk $$\xi_{k+1} = \xi_k + \nu$$ 로 모델링하고 확장 칼만 필터로 오도메트리에서 추정해 학습 라벨을 자동 생성한다. WayFAST의 moving-horizon 추정기에서 영감을 받았다.

### 지형 특징 처리: DINOv2 + 오토인코더 + 격자지도

RGB에서 DINOv2 백본으로 픽셀별 384차원 특징을 뽑는다. 이 차원을 그대로 격자지도에 투영하면 연산·메모리가 폭발하므로, 대규모 off-road 데이터로 재구성 손실을 비지도 학습한 오토인코더로 저차원(실험에서 $$p=3$$)으로 압축한다. LiDAR 점군은 elevation grid map으로 처리하고, 시각과 기하를 합쳐 Multi-Modal Elevation Mapping 기반 multi-modal grid map(0.25m 해상도, 20Hz)을 만든다. 네 바퀴 접지점 아래의 지형 특징 $$\phi$$ 를 추출해 데이터셋 $$D = \{(\phi_j, v_j, \xi_j)\}$$ 를 구성한다.

### 불확실성 인지 예측: 확률적 앙상블

동일 구조의 MLP([16,32,32,16] + LeakyReLU) $$M=5$$ 개를 독립 학습한다. M명의 전문가에게 같은 질문을 던져 평균과 의견 불일치를 보는 셈이다. 불확실성은 두 종류로 나뉜다.

$$
\sigma^2 = \sigma^2_{ale} + \sigma^2_{epi}
$$

aleatoric은 각 멤버가 출력하는 $$\sigma^2$$ 의 평균(데이터 고유 잡음), epistemic은 멤버 간 평균 예측의 분산(지식 부족)이다.

### 생성형 경험 회상 모델 R: CVAE

사진첩(데이터)을 버려도 그 시절을 그려내는 화가(생성기)를 남겨두면 과거를 떠올릴 수 있다는 발상이다. 속도 $$v$$ 는 하드웨어 한계 안의 물리량이므로 먼저 $$v$$ 를 샘플링하고, 그에 조건부로 지형 특징 $$\phi_{recall}$$ 을 디코더 $$p_{dec}(\phi \mid v, z)$$ 로 생성한다. 회상된 입력 $$(\phi_{recall}, v_{recall})$$ 을 예측기 $$T$$ 에 통과시켜 출력 $$\xi_{recall}$$ 과 그 불확실성 $$\sigma_{recall}$$ 을 함께 얻는다. $$\sigma_{recall}$$ 이 크면 "이 회상 샘플은 지금까지의 학습 데이터에서 잘 표현되지 않던 영역"이라는 신호다.

### 불확실성 인지 적응: 두 모델의 동시 갱신

배포 $$i$$ 에서 새 데이터 $$D_i$$ 와 회상 데이터 $$D_{recall}$$ 로 두 모델을 갱신한다.

**예측기 $$T$$ 갱신:**

$$
\mathcal{L}_{T_i} = \mathcal{L}_{NLL}(D_i) + \lambda \, \mathcal{L}_{adapt}(D_{recall})
$$

첫 항은 새 환경 적응(음의 로그 우도), 둘째 항은 과거 보존이다. $$\mathcal{L}_{adapt}$$ 는 Jensen-Shannon divergence(대칭 KL)로 예측 분포와 회상 분포를 정렬해 망각을 완화하고, $$\sigma_{recall}$$ 이 큰 불신 샘플에 대한 과적합을 막는다.

**회상기 $$R$$ 갱신:** 회상 샘플 중 분산 임계값 $$\tau$$ 로 필터링해 신뢰 샘플만 남긴다.

$$
\bar{D}_{recall} = \{(\phi_{recall}, v_{recall}) \mid \sigma_{recall} < \tau\}, \quad D_{aug} = D_i \cup \bar{D}_{recall}
$$

$$D_{aug}$$ 로 $$R$$ 을 재학습한다. 믿을 만한 과거만 보존에 기여하고, 새 데이터는 적응에 기여한다.

### 항법: 샘플링 기반 MPC

갱신된 $$T$$ 를 MPC에 넣는다. 비용은 다음과 같다.

$$
C = w_1 \cdot \text{Goal} + w_2 \cdot \sigma^2_t + w_3 \cdot |1 - \mu_t|
$$

Goal은 목표 진행, $$\sigma^2_t$$ 는 불확실 영역 회피(OoD 회피의 핵심), $$|1-\mu_t|$$ 는 traction을 1(이상적 무미끄럼)에 가깝게 하는 항이다. 확률적 동역학은 mean-propagation으로 근사하고, horizon $$T=30$$(3초), 1024 rollouts, 10Hz로 동작한다.

## 실험과 결과

**플랫폼:** Clearpath Jackal(skid-steering), NVIDIA Jetson AGX Orin 온보드 연산, Ouster LiDAR, ZED X 스테레오 카메라, LiDAR-inertial odometry. 전체 ROS 2.

**데이터셋:** 5개 환경 — $$D_1$$ 공원 아스팔트, $$D_2$$ 강변 자갈, $$D_3$$ 모래, $$D_4$$ 자전거길, $$D_5$$ 빽빽한 숲. 각 사이트에서 주행 가능·불가 영역을 모두 포함해 약 10분 수동 주행했다. PCA로 5개 환경 간 뚜렷한 domain shift를 시각화했다.

**지표:** Adaptation은 $$D_j$$ 학습 직후 $$D_j$$ 테스트 NLL($$n_{j,j}$$, 낮을수록 좋음). Memory retention은 Forgetting Measure(FM) $$f_{k,j} = \max(0, n_{k,j} - \min_{i<j} n_{k,i})$$ 로, 낮을수록 보존이 우수하다.

**베이스라인 6종:** CM(전체 데이터 재학습, 상한), IMOST(점증 메모리), WVN(빠른 적응만), LwF(증류), NGR(본 방법에서 $$\tau$$ 불확실성 필터만 제거한 ablation), Proposed. 공정성을 위해 모든 베이스라인을 동일 입력·확률적 출력으로 인터페이스하고 Pareto front에서 하이퍼파라미터를 골랐다. 통계는 10회 학습 세션 평균이다.

핵심 결과 세 가지는 다음과 같다.

1. **과거 데이터 저장 없이 망각 최소화.** 과거 데이터에 직접 접근하지 않는 방법 중 제안 방법의 FM이 가장 낮다. 예를 들어 $$D_1$$ 을 $$D_5$$ 까지 학습한 뒤 보존 FM은 1.45±0.13으로 WVN 2.56, LwF 2.68, NGR 1.82보다 낮고, 메모리가 계속 커지는 IMOST(1.55)와 대등하거나 더 좋다.
2. **불확실성 필터링의 효과 입증.** 필터를 뺀 NGR은 회상 샘플이 실제 분포에서 크게 흩어지는 반면(PCA), 제안 방법은 실제 데이터 분포에 밀착한다. 정량적으로도 NGR 대비 전 구간에서 FM이 개선된다.
3. **OoD 회피 항법 성공.** $$D_1$$ 부터 $$D_5$$ 까지 순차 학습한 $$T_5$$ 로 환경 1에 복귀해 주행하자, 목표 도달 3/3, OoD(잔디) 회피 3/3으로 상한 CM(3/3, 3/3)과 동등했다. IMOST, WVN, LwF는 OoD 회피 0/3, NGR은 1/3에 그쳤다. 망각 때문에 OoD 불확실성을 잘못 추정해 위험 영역에 들어간 것이다.

한 가지 짚을 점은 adaptation 단독 성능이다. 제안 방법은 항상 1등이 아니며, 예컨대 $$D_1 \to D_2$$ 에서 NLL 0.71로 WVN의 0.13보다 나쁘다. 새 환경에 즉시 적합하는 데는 빠른-적응 방법이 유리할 수 있지만, 보존(FM)에서의 우위로 종합 성능이 앞선다. 논문도 이 트레이드오프를 인정한다.

## 결론과 의의

야지 traversability 연속학습에서 "데이터 저장 없는 생성형 재생 + 생성 샘플 불확실성 게이팅"이라는 조합을 실로봇에 통합·검증한 것이 분야 기여다. 메모리 비용을 예측기와 회상기 파라미터만으로 고정해 환경 수와 무관하게 장기 배포가 가능하다는 점이 실용적 핵심이며, traversability를 traction으로 물리적으로 정의하고 MPC와 결합해 예측 불확실성이 곧바로 위험 회피 항법으로 이어지는 닫힌 루프를 보였다.

로봇 실무 관점에서 정리하면 다음과 같다.

- **쓸 수 있는 곳:** 실외 비정형 지형 주행 로봇이 배포 지역을 옮겨 다니며 오랜 기간 운용될 때. 임베디드급(Jetson AGX Orin)에서 앙상블 5개, 1024 rollouts로 10Hz 추론이 되므로 온보드 연속학습의 현실적 경로다.
- **재현 난이도는 상:** 데이터셋과 체크포인트가 미공개라 실데이터를 직접 수집해야 하고, Jackal·Ouster·ZED X·elevation mapping 풀 하드웨어 스택에 의존하며, DINOv2 오토인코더는 별도 대규모 off-road 데이터로 사전학습을 가정한다. 리포지토리에 LICENSE 파일이 없어 라이선스는 확인되지 않았으므로 코드 재사용 전 저자 확인이 필요하다. 핵심 알고리즘(CVAE 회상 + $$\tau$$ 필터 + JS 정렬)만 합성 데이터로 재현하는 것은 중 수준이다.
- **회상 품질이 예측기 불확실성에 의존:** $$\tau$$ 필터는 선행 예측기의 $$\sigma$$ 추정이 정확하다는 가정에 기댄다. epistemic 추정이 빗나가면 잘못된 샘플을 보존할 위험이 있다.
- **표현력과 규모의 한계:** 환경 수와 지형 다양성이 매우 커질 때 단일 CVAE가 모든 과거 분포를 충실히 합성할 수 있는지는 5개 환경까지만 실증됐다. 항법 trial은 방법당 3회, OoD 시나리오는 잔디 회피 하나로 통계적 일반화에는 부족하다. $$\tau, \lambda$$ 등의 환경별 민감도와 자동 조정도 미해결이다.

## 참고

- 논문: [DOI 10.1109/LRA.2025.3619687](https://doi.org/10.1109/LRA.2025.3619687) · 코드: [github.com/HMCL-UNIST/Continual-Traversability-Learning](https://github.com/HMCL-UNIST/Continual-Traversability-Learning)
- Gasparino et al., WayFAST: Navigation with Predictive Traversability in the Field (2022)
- Frey et al., Fast Traversability Estimation for Wild Visual Navigation (2023)
- Ma et al., IMOST: Incremental Memory Mechanism with Online Self-Supervision for Continual Traversability Learning (2024)
- Wang et al., A Comprehensive Survey of Continual Learning (2024)
- Sohn, Lee, Yan, Learning Structured Output Representation using Deep Conditional Generative Models (2015)
