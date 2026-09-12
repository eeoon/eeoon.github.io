---
layout: page
title: Go2 다층 건물 시뮬레이션 테스트베드와 VLM 자율 미션 에이전트
description: 계단·경사·동작 엘리베이터가 있는 3층 건물 월드를 Gazebo Harmonic → Isaac Sim 5.0 → MuJoCo로 이식하며 사족보행 제어·실사양 센서·VLM 오케스트레이션 에이전트를 검증한 테스트베드
importance: 8
category: company
---

**기간** 2026.06 ~ 현재 · **소속** KETI (SDR 사족보행 확장 트랙 · 다중협력주행 연계) · **역할** 월드 제작·이식 파이프라인, 보행 제어·정책 이식, 센서 모사, VLM 에이전트 실험

## 배경과 목표

사족보행 로봇 **Unitree Go2**가 계단·경사로·동작하는 엘리베이터를 갖춘 다층 실내 건물을 이동하는 시나리오를 실기 투입 전에 검증할 테스트베드가 필요했다. 목표는 ① 다층 건물 공용 월드 확보 ② 보행 제어·RL 정책의 시뮬레이터 간 이식 체계 ③ 실사양 센서 기반 인지 검증 ④ 상위 자율성(VLM 에이전트) 실험대 확보다.

## 시뮬레이터 3트랙

| 항목 | Gazebo Harmonic | Isaac Sim 5.0 / IsaacLab | MuJoCo |
| --- | --- | --- | --- |
| GPU | 불필요 | RTX 필수 | 불필요 |
| 월드 원본 | **SDF 정본** — 절차적 3층 건물 생성기 | SDF → USD (메타데이터 계약) | SDF → MJCF 멱등 변환 스크립트 |
| 엘리베이터 | 동작 플러그인 | GPU 상주 FSM | FSM + 방문·보행자 모사 |
| 보행 제어 | 자작 트롯/크롤 · OCS2 · CHAMP 4종 정량 비교 | IsaacLab RL 학습·구동 | 동결 정책 2종(PGTT flat · IsaacLab uneven) + 어댑터, 하이트스캔 기반 지형 자동 전환·stair assist |
| 센서 | 표준 플러그인 | 지형 베이킹으로 물리–인지 일치, 표준 ROS 2 토픽(Jazzy) | Mid-360 비반복 스캔·intensity, D455 깊이 열화 등 **실사양 모사** |
| 적합 용도 | 월드 제작·저비용 통합 검증 | RL 학습·고충실 인지 | 경량 반복 실험·정책 이식·에이전트 실험대 |

## 핵심 성과

- **공용 월드 이식 계약:** 한 번 만든 SDF 건물을 USD·MJCF로 갈아타는 파이프라인을 확립해 세 물리 엔진에서 재사용.
- **정책 이식 체계:** 학습(IsaacLab / MJX)과 실행(MuJoCo)을 분리하고, 동결 정책 + 어댑터 + 지형 기반 자동 전환(하이트스캔 z-range, 즉시/3 s 비대칭)으로 지상 → 2층 계단 완주. 테스트 90종의 자족 패키지로 검증.
- **VLM 오케스트레이션 자율 미션 에이전트:** "판단은 VLM, 사실은 센서" 원칙으로 결정론적 도구층 위에 로컬 27B VLM(단일 GPU)을 얹어, 사전 지도 없이 자연어 미션만으로 엘리베이터를 발견·탑승해 2층에 도달(7턴 완전 성공).
- 이족보행 플랫폼 LimX TRON1의 SDK 구조 분석과 시뮬↔실기 공용 Low-level API 제어 검증도 병행했다.

## 관련 리뷰

MuJoCo 트랙의 flat 정책은 [PGTT](/blog/2026/pgtt/) 논문의 공개 정책을 이식한 것이며, 상위 자율성 실험은 [NaVILA](/blog/2026/navila/) 계열 legged VLN 연구와 맞닿아 있다.

## 기술 스택

Unitree Go2 · ROS 2 Humble / Jazzy · Gazebo Harmonic · Isaac Sim 5.0 / IsaacLab · MuJoCo / MJX · SDF / USD / MJCF · OCS2 · CHAMP · RL 정책 어댑터 · 로컬 VLM · Python / C++
