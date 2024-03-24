---
layout: post
title: ROS
description: >
  ROS1 vs ROS2
sitemap: false
hide_last_modified: true
---

# ROS1 / ROS2

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/DevOps/images/ros/image.png)

## ROS(Robot Operating System)

**로봇 운영체제**(ROS,Robot Operating System)는 로봇 응용 프로그램을 개발할 때 필요한 하드웨어 추상화, 하위 디바이스 제어, 일반적으로 사용되는 기능의 구현, 프로세스간의 메시지 패싱, 패키지 관리, 개발환경에 필요한 라이브러리와 다양한 개발 및 디버깅 도구를 제공한다. ROS는 로봇 응용 프로그램 개발을 위한 운영체제와 같은 로봇 플랫폼이다. 하드웨어 플랫폼을 하드웨어 추상화로 포함하고 있으며, 로봇 응용 소프트웨어 개발을 지원을 위한 소프트웨어 플랫폼이면서 이기종의 하드웨어에서 사용 가능한 운영체제와 같은 기능을 갖추고 있다.


<span style="color:#808080">출처 : [로봇_운영체제_ROS](https://ko.wikipedia.org/wiki/%EB%A1%9C%EB%B4%87_%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C)</span>


참고,
<span style="color:#808080">[ROS: Home](https://www.ros.org/)</span>


## ROS1 과 ROS2의 차이

### ROS1

- Linux 환경 - *Ubuntu 20.04 지원 (noetic)*
- 단일 로봇
- 실시간 제어 미지원
- 안정된 네트워크 환경 요구
- 대학, 연구소의 연구 용도

### ROS2

- 다중 노드 통신
- 임베디드 시스템에서 ROS사용
- 실시간 제어
- 불안정한 네트워크 환경 커버 가능
- 멀티 플랫폼 (Windows, Linux, MacOS) 지원
- 최신 기술 지원 (Protocol Buffers, DDS, Websockets 등)
- 상업용 제품 지원

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/DevOps/images/ros/image1.png)

<span style="color:#808080">출처 : [https://www.lips-hci.com/ros](https://www.lips-hci.com/ros)</span>

## 결론

- ROS1은 2020년 이후로 공식 릴리즈버전이 없으며 ROS2는 Foxy, Humble, Rolling 등 공식 릴리즈 버전이 제공되고 있다.
- ROS2 는 ROS1 에서의 큰 단점인 실시간 제어가 가능하다는 장점이 있다.
- 커뮤니티에서도 현재 ROS2에 대한 관심을 갖고 있으며 RosCon에서도 ROS2를 기준으로 언급함.
- ROS1에서는 Catkin_make를 이용해 소스코드를 build 하지만 ROS2에서는 colcon 을 활용해 build한다.
- ROS2에서는 다양한 네트워크 환경(DDS/RTPS) 등을 활용하여 유기적인 통신이 가능하다.
- ROS1에서는 Master-Slave 구조로 연결되어 있다면 ROS2에서는 Master Node가 없기 때문에 PTP(peer to peer) 형태로 다양한 데이터 타입을 효과적으로 교환할 수 있다.

*[HTML]: HyperText Markup Language
*[CSS]: Cascading Style Sheets
*[JS]: JavaScript
