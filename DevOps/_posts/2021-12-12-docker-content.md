---
layout: post
title: Docker
description: >
  Docker
sitemap: true
changefreq : daily
priority : 1.0
hide_last_modified: true
---

# Docker

![docker-logo-blue.png](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/DevOps/images/docker/image.png)

- 도커(Docker)는 리눅스 응용 프로그램을 프로세스 격리 기술들을 사용해 컨테이너로 실행하고 관리하는 컨테이너 기반 가상화 플랫폼 소프트웨어를 환경에서 격리된 단위로 패키징하고 배포할 수 있다.

- 도커 컨테이너의 장점으로 소프트웨어 실행에 필요한 모든 것을 포함하는 완전한 파일 시스템 안에 wrapping 되어 있기 때문에 실행중인 환경에 관계 없이 언제나 동일하게 실행되는 결과가 나타난다.

- 도커 이미지
    - 도커 이미지는 애플리케이션을 실행하는데 필요한 모든 것을 포함하고 있는 파일 시스템
    - Dockerfile을 사용하여 이미지를 정의하고 도커 CLI를 사용하여 빌드할 수 있다.
- 도커 컨테이너
    - 도커 이미지의 인스턴스로, 파일 시스템과 애플리케이션 실행환경을 가지고 있다.
    - 컨테이너는 독립적이지만 호스트 시스템의 리소스를 공유할 수 있다.
- 도커 레지스트리
    - 도커 이미지를 저장하고 공유하는데 사용되는 저장소
    - Docker Hub는 가장 널리 사용되고 있는 플랫폼
- 도커 컴포즈
    - 도커 애플리케이션을 정의하고 실행하기 위한 도구
    - YAML 파일을 사용하여 여러 컨테이너를 정의하고 단일 명령어로 애플리케이션을 시작할 수 있다.
- 도커 스윔
    - 도커 클러스터링과 오케스트레이션 도구, 여러 호스트에서 도커 컨테이너를 관리하고 배포할 수 있다.
    - swarm을 사용하여 서비스를 확장하고 높은 가용성을 확보

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/DevOps/images/docker/image1.png)

## docker command

자주 사용하는 docker command 에 대한 설명

`docker pull image_name`

Docker Hub 또는 다른 Docker 레지스트리에서 이미지를 가져온다.

`docker commit container image_name` 

실행중인 컨테이너를 이미지로 커밋한다. (컨테이너를 이미지화)

`docker run --options` 

docker image에서 container를 생성하고 실행하는데 사용된다. 여러 옵션들이 있다.

-d : container를 백그라운드에서 실행하도록 지정

-v : host와 container의 디렉토리를 공유

-e : container 내부의 환경변수를 설정

--name : 실행할 container의 이름을 지정

-it : container를 인터랙티브하게 실행

etc..

 

`docker ps`

docker 환경에서 실행중인 container list를 나타낸다. 

`docker ps -a`

docker 환경의 전체 container list를 나타낸다.

`docker image list`

Docker image list를 볼 수 있다.

`docker rm container_name`

Docker container 를 삭제할 수 있다.

`docker rm image_name`

Docker image를 삭제할 수 있다.

`docker exec -it container_name /bin/bash`

Docker에서 실행중인 container 내부에 인터랙티브한 bash 셸이 실행되어 접속할 수 있다.

`docker attach container_name`

실행중인 container에 연결하여 터미널을 제어한다.

`docker start container_name`

`docker stop container_name`

container를 시작하거나 중지할 수 있다.

## local에서 DISPLAY 공유

`xhost +local:`

## 결론

- 도커의 장점으로 개발 환경, 테스트 환경, 프로덕션 환경 등을 일관되게 유지할 수 있다.
- 가상화된 컨테이너는 가볍고 빠르게 시작되므로 자원을 효율적으로 사용할 수 있다.
- 컨테이너는 어디서든 동일하게 실행될 수 있어 이식성이 뛰어나다.


*[HTML]: HyperText Markup Language
*[CSS]: Cascading Style Sheets
*[JS]: JavaScript
