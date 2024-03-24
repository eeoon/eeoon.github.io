---
layout: post
title: Computer_Vision
description: >
  Computer Vision, Autonomous Driving, Image processing
sitemap: true
changefreq : daily
priority : 1.0
hide_last_modified: true
---

# Computer Vision 

**Vanishing Point & Perspective Transform**

<!-- *2023년* 

- start
-->
## Vanishing Point & Perspective Transform

### 영상처리를 통한 자율주행

**project propose : Vanishing point를 활용하여 주행 방향을 결정할 때 발생되는 오차를 Lane detection을 통해 보안**

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled.png)

- 영상 처리 과정에서 라인을 검출하여 만나는 지점의 소실점(Vanishing Point)를 활용하여 주행방향(조향)을 결정한다.
- 소실점이 생성되는 과정에서 발생되는 오차는 박스를 설정하여 소실점이 박스를 벗어났는지 판단하는 과정을 진행한다.
- 카메라가 인식하는 2차원을 Perspective Transform의 과정을 거쳐 라인을 검출하였다.
- 카메라를 통해 검출된 소실점과 라인 검출을 통해 새로운 해석을 도입한다.

## Vanishing Point

3D 공간에서 평행한 직선은 2D 영상에서 FOV(Field of View)로 인하여 평행하게 보이지 않는다. 간단한 예시로 도로위의 차선은 bird of view로 바라보면 평행하나 운전자의 입장(카메라)에서 바라보면 평행하지 않게 된다. 

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled.png)

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_1.png)

basic of concept
{:.figcaption}

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_2.png)

실제 이미지에 적용한 Vanishing point로 lane을 검출하여 lane의 접점을 red dot으로 표시하였다.
{:.figcaption}

## Perspective Transform

관심영역과 평행이 아닐 때 수집한 영상을 대상체와 평행인 것 처럼 보정처리하는 방법이다. 4개의 POINT를 설정하여 해당영역의 비율을 조정하여 이미지의 좌표를 변환처리할 수 있다.

| ![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/image_1.png) | ![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/image_2.png)

Perspective Transform을 임의의 영상에서 진행하면 다음과 같이 Fied of View를 변경하여 region of interest(ROI)를 lane으로 변경한 것이다.

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_4.png)

camera filed of view를 45 degree / 90 degree 로 진행하였을 때 어떤 방향이 더 효율적인가를 처음에 프로젝트 시작할때 고민하여 테스트를 진행

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_5.png)

algorithm of project (flow chart)

## 결과

### Vanshing Point

Box 크기를 조절하여 영상 내부에 소실점의 범위를 규정
- Gaussian Distribution 공식 적용

- bounding box의 타당성을 검증하기 위해 red dot 이 존재하는 영역의 좌표를 확인 후 Gaussian Distribution을 진행한 결과

Hough Transform Line Detection을 통해 Vanishing point 검출

(Hough Transform Line Detection 추가 설명)


| ![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_6.png) | ![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_7.png) |



result of Gauissian Distribution for red dot.

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_8.png)

### Perspective Transform

변환 전 4개의 좌표를 직선 구간에서 선정

- 차량이 차선의 중앙이 아닐 경우 판단 가능.
범위를 벗어날 경우 Text 출력으로 현재 주행상태 시각화

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_9.png)

두 라인이 검출되면 parallel 하다고 판단한다.

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_10.png)

## **Vanishing Point & Perspective Transform 결합**

### 두 기법의 상호 보완 판단 지표

- Confusion matrix 참고하여 발상

- 오류성 주행의 판단 지표

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_11.png)

 - 영상에서 각각 [In/Out], [Parallel/Bias] 출력
 - Console에서 위 내용을 결합하여 [직진구간 중앙주행 / 직진구간 치우침, 회전구간 중앙주행 / 회전구간 치우침] 출력

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_12.png)

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_13.png)
## 결론

- Bounding Box를 통해 Vanishing point의 직진/회전 구간을 판단.
- Perspective transform  Lane detection을 적용하여 차량의 치우침을 판단.
- 위 두 조합의 상호보완을 통해 주행 안정성을 향상하는 기법을 제안.
- 오픈된 주행 영상 데이터셋을 이용하여 제안된 방법을 검증.


적용 가능성

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Untitled_14.png)




참고 Refrence Paper
{:.faded}


[vanishing paper.pd.pdf](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/vanishing paper.pd.pdf)

[TIP2012.pdf](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/TIP2012.pdf)

[Vanishing_Point_Detection_WACV2017.pdf](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/project/images/computer_vision/Vanishing_Point_Detection_WACV2017.pdf)


*[HTML]: HyperText Markup Language
*[CSS]: Cascading Style Sheets
*[JS]: JavaScript
