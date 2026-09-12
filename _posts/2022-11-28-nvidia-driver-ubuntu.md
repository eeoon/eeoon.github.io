---
layout: post
title: "Ubuntu 22.04 NVIDIA 그래픽카드 인식 문제 해결"
date: 2022-11-28 09:00:00 +0900
description: "Ubuntu 22.04 설치 후 NVIDIA 드라이버 인식 오류를 해결하는 절차"
tags: [nvidia, ubuntu, devops]
categories: tech-note
giscus_comments: false
related_posts: false
---

## ubuntu 22.04 설치 후 nvidia_drive(graphic card) 설치

* 먼저 ubuntu22.04를 설치하고 실행하였는데 그래픽 카드 인식 오류가 발생한다면 PC 뒤에서 Display Port (or HDMI) 를 그래픽카드가 아닌 마더보드에 연결할 수 있도록 하고 재부팅한다. 그리고 terminal 창을 열어서 그래픽 카드에 적합한 드라이버를 검색한다.

`sudo ubuntu-drivers devices`

![Untitled1](/assets/img/blog/devops/nvidia/image.png)

드라이버 중 추천해주는 모델을 설치를 진행한다.

`sudo apt install nvidia-driver-{number}`

nvidia 드라이버 모델 설치가 완료된 것을 확인한다.

`nvidia-smi`

![Untitled2](/assets/img/blog/devops/nvidia/image1.png)

정상적으로 진행이 되었다면 nvidia 드라이버가 설치가 되었으니 그래픽카드로 부터 HDMI를 연결해 디스플레이를 확인할 수 있다.
