---
layout: post
title: Git과 GitHub 기초: 설치부터 push까지 완전 정리
description: >
  Git 분산 버전 관리 시스템의 기본 개념부터 Ubuntu 설치, clone, push까지 실전 명령어를 정리합니다.
sitemap: true
changefreq : daily
priority : 1.0
hide_last_modified: true
---

# Git

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/DevOps/images/git/image.png)

- Git은 분산 버전 관리 시스템(Distributed Version Control System, DVCS) 중 하나로, 소스 코드와 같은 파일의 변경 사항을  추적하고 관리하는 도구
- 파일의 변경사항을 추적하여 이전 상태로 롤백하거나 변경 내용을 비교할 수 있다. 또한 여러 개발자 간의 협업을 용이하게 하며 변경사항의 충돌을 관리
- 브랜치를 통해서 여러 작업을 병렬로 처리할 수 있다. 각 브랜치는 개별적으로 관리되며 변강사항을 통합할 수 있다.
- 원격 저장소를 통해 다른 개발자와 협업하고 코드를 공유할 수 있다. 원격저장소로는 Github, GitLab, Bitbucket 등이 있다.

## Ubuntu 에 git 설치

`sudo apt install git`

Ubuntu에 git을 설치하는 CLI는 다음과 같다.

`git --version`

설치가 완료되었다면 설치된 Git 버전을 다음 CLI를 통해서 확인 할 수 있다.

## github에서 clone하기

<p align="center">
</p>

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/DevOps/images/git/image2.png)




`git clone 클론하고자 하는 래퍼지토리`

ex, `git clone [https://github.com/eeoon/eeoon.git](https://github.com/eeoon/eeoon.git)`

옵션을 통해서 원하는 branch를 clone할 수 있다.

`git clone https://github.com/eeoon/eeoon.git -b humble`

## github에 push하기

[GitHub: Let’s build from here](https://github.com/)

- 내 github에 push하기 위해서는 사전작업으로 Github에 가입하여 내 repository를 생성하고 해당 repository로 내가 작업중이던 코드, 프로젝트 등을 push 할 수 있다.

- Github에 가입하였다면

    `git config --global user.name "Your Name"`

    `git config --global user.email "your.email@example.com"`

    명령을 진행한다.


- 사용자의 이름, 이메일 주소를 설정한다. option으로 global로 설정되어 있기 때문에 전역적으로 설정되었고 local설정으로 변경할 때에는 해당 옵션 없이 진행하면 된다. 현재 전역으로 설정되었기에 추가적으로 반복해서 진행할 필요는 없다.

## Github Repositry 생성

![Screenshot1](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/DevOps/images/git/image3.png)

회원가입이 완료되었다면 your Repositories에 클릭하여 우측 상단의 New를 클릭한다.

다음과 같은 화면이 나타나면 Repository name을 작성하고 옵션으로 Description, Properties 등을 설정한다.

![Screenshot2](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/DevOps/images/git/image4.png)



만들어진 repository를 확인한 후 우측 상단의 Code를 누른 후 url를 Copy 한다.

![Screenshot3](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/DevOps/images/git/image5.png)

내 Desktop에서 Github에 올릴 repository에 접속하여 다음의 명령을 수행한다.

`git init`

로컬에서 Github에 올릴 프로젝트 디렉토리에서 Git을 초기화

`git add .`

프로젝트 파일을 Git으로 관리하기 위해 파일 추가 및 커밋

`git commit -m “커밋 메시지”`

커밋할 메시지를 추가하여 커밋을 진행한다.

이때 커밋할 메시지는 git push 되었을때 변경사항에 대한 간략한 설명을 뜻한다.

`git remote add origin https://github.com/사용자이름/저장소이름.git`

Github에 생성한 원격저장소를 로컬 Git에 추가한다.

여기서 위 사진에서 복사한 url을 입력한다.

ex,

`git remote add origin https://github.com/eeoon/example.git`

`git checkout -b main`

branch를 변경한다. 새로운 branch를 생성하고 해당 branch로 전환할 수 있다.

`git branch -m master main`

`git fetch origin`

`git branch -u origin/main main`

만약 branch가 다른곳(master)이라면 이름을 변경해줘야한다.

혹시나 위의 방법이 안된다면 아래의 방법을 진행

`git checkout -b main`

`git checkout main`

`git merge origin/main`

`git push origin main`

로컬의 변경 사항을 원격 저장소(Github)에 푸쉬한다.

만약 마지막 단계를 진행했을때도 실패한 경우에는

`git push origin +main`

입력하여 강제로 git push 할 수 있다.

git push가 정상적으로 진행되었다면 다음과 같은 결과를 볼 수 있다. (commit message를 picture로 설정)

![Screenshot4](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/DevOps/images/git/image6.png)


*[HTML]: HyperText Markup Language
*[CSS]: Cascading Style Sheets
*[JS]: JavaScript
