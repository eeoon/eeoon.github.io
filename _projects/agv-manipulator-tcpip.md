---
layout: page
title: AGV–매니퓰레이터 TCP/IP 통합 통신 프로토콜 (제조물류)
description: 상위 제어(MasterPC) ↔ ACS 서버 ↔ 스태커 로봇을 잇는 TCP/IP 커스텀 프로토콜 설계 — STX/CR/LF 프레이밍, 명령 ID 매칭, ACK/NACK + Reason 코드, HeartBeat
img: assets/img/projects/agv-robotarm-tcpip/img-1.png
importance: 6
category: company
---

**기간** 2024.05 ~ 2024.09 · **소속** KETI (협업지능 기반 로봇플러스 경쟁력 지원 사업) · **역할** 통신 프로토콜 설계, TCP/IP 통신 모듈·ACS 통신 매니저 구현, 패킷 구조 정의·문서화

## 배경과 문제

제조물류 자동화에서는 상위 제어 시스템(MasterPC)과 현장 로봇(스태커 AGV) 사이에서 작업 명령 전송과 상태 모니터링이 끊김 없이 이뤄져야 한다. 서로 다른 제어 체계를 잇는 통신 규격이 필요했고, 단순 중계가 아니라 양방향 통신 허브로서 명령 일관성과 신뢰성을 확보하는 것이 목표였다.

## 접근

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/agv-robotarm-tcpip/img-1.png" title="전체 통신 구조" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/projects/agv-robotarm-tcpip/img-2.png" title="Control Master 통신 구조" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">전체 통신 구조(왼쪽)와 Control Master 통신 구조(오른쪽).</div>

- **아키텍처:** `MasterPC ↔ TCP/IP ↔ ACS Server ↔ TCP/IP ↔ Stacker`. ACS Manager가 Server(Master 요청 수신 → ACS 전달)와 Client(ACS 응답 → Master 전달)를 동시에 수행하는 이중 구조.
- **패킷 프레이밍:** 모든 패킷은 STX(0x02)로 시작해 CR/LF로 끝나고, 그 사이에 CMD 필드와 데이터 필드를 둔다. CMD의 대소문자로 송수신 방향을 구분한다(작업명령 o/O, Pause/Resume p/P, 상태보고 s/S, HeartBeat h/H).
- **주요 기능:** ① Pick/Place 작업 명령(우선순위 1~9, Command ID) ② ACK/NACK + 9종 Reason 코드로 실패 원인까지 진단하는 응답 ③ Pause/Resume ④ ASSIGN ~ ORDER COMPLETE 7단계 작업 상태 보고 ⑤ AGV별 HeartBeat.
- **설계 특징:** 시각 기반 Command ID로 비동기 환경에서 응답을 매칭하고, 단순 성패를 넘어 실패 원인을 진단할 수 있게 했다.

## 결과

작업 명령부터 상태 모니터링·연결 확인까지 통신 라이프사이클 전반을 아우르는 규격으로 상위 제어와 현장 로봇 간 안정적인 통신 인프라를 구축했다. 스태커 AMR 운용(MCP·VehiclePlanner·ACS·QR 정밀 도킹)과 MiR 600 프로그래밍 등 이종 물류로봇 통합의 통신 기반이 되었다.

## 기술 스택

TCP/IP · Client/Server 하이브리드 · STX/CR/LF 패킷 프레이밍 · ACK/NACK 프로토콜 · C++ / C · 스태커 AMR · ACS
