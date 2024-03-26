---
layout: post
title: Gazebo
description: >
  Gazebo Simulator
sitemap: true
changefreq : daily
priority : 1.0
hide_last_modified: true
---

# Gazebo

![Gazebo_BlockDiagram](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/gazebo/image.png)

Gazebo_BlockDiagram

출처 : [https://classic.gazebosim.org/tutorials](https://classic.gazebosim.org/tutorials)

**위조금 더 간략하게 그림을 그리자면 아래와 같다.**

![Architecture-of-ROS-Gazebo-interaction-Mittal-2018.png](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/gazebo/image2.png)

출처 : [https://www.researchgate.net/figure/Architecture-of-ROS-Gazebo-interaction-Mittal-2018_fig2_352871117](https://www.researchgate.net/figure/Architecture-of-ROS-Gazebo-interaction-Mittal-2018_fig2_352871117)

# What is Gazebo?

![gazebo_horz_pos.png](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/gazebo/image3.png)

- Gazebo는 **Robotics Simulator**의 한 종류로 오픈소스 3D Simulator.
- Robot의 motion을 **simulation**하고 **visualize** 하는데 사용된다.
- Gazebo는 **ROS**와 통합되어 있어 ROS를 사용하는 대다수, 대부분의 프로젝트에서 사용되며 하나의 시뮬레이터 소프트웨어 프레임워크로 발전해 왔다.
- Gazebo의 장점으로 다양한 유형의 **Robot**, **Sensor**, **Environment**를 지원하며 사용자는 자신만의 로봇을 만들수도, 환경을 설정할 수도 있다. 또한 시뮬레이션 중 Robot을 Control하고 Sensing 할 수 있어 많은 데이터를 얻을 수 있다.
- 다양한 환경 구성 중 조명, 햇빛, 그림자, 텍스처, 수중환경, 등 현실적인 환경 렌더링을 제공한다.
- 2017년 Gazebo Classic과 함께 **Ignition**이 등장하였다. (자주 사용해보지는 않았으나 프레임워크가 조금 더 가볍다는 장점이 있는 것 같다.)
- Gazebo Classic 에서는 ODE 물리 엔진, OpenGL 렌더링, 그리고 Sensor Simulation, Actuator Control을 위한 지원 코드를 통합하며 고성능 물리 엔진을 사용할 수 있도록 함

![참고 사진 : aws_warehouse ([https://github.com/aws-robotics/aws-robomaker-small-warehouse-world](https://github.com/aws-robotics/aws-robomaker-small-warehouse-world))](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/gazebo/image4.png)

참고 사진 : aws_warehouse ([https://github.com/aws-robotics/aws-robomaker-small-warehouse-world](https://github.com/aws-robotics/aws-robomaker-small-warehouse-world))

## Gazebo환경을 구성하는 것을 파악해보자

Gazebo 환경을 구성하는 것은 크게 환경(**World**)와 그 안에 존재하는 로봇, 센서 등 **Model(sdf,urdf,xacro)** 가 존재한다.

[출처 : [https://www.researchgate.net/figure/Gazebo-Robotic-Operating-System-ROS-interface-block-diagram-28_fig4_350175271](https://www.researchgate.net/figure/Gazebo-Robotic-Operating-System-ROS-interface-block-diagram-28_fig4_350175271)](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/gazebo/image5.ppm)

출처 : [https://www.researchgate.net/figure/Gazebo-Robotic-Operating-System-ROS-interface-block-diagram-28_fig4_350175271](https://www.researchgate.net/figure/Gazebo-Robotic-Operating-System-ROS-interface-block-diagram-28_fig4_350175271)

## World

world 파일을 보면 기본적으로 XML 형식으로 작성되며 로봇 및 다양한 객체의 위치, 모양, 센서, 광원, 물리적 특성 등을 포함하여 시뮬레이션 환경을 설명한다.

- **Model** : 환경에 있는 로봇, 센서 등을 설명하는데 사용 각 모델 위치, 회전, 링크 조인트 등으로 정의
- **Physics Properties** : 시뮬레이션에서 물리적 특성으로 중력, 마찰, 충돌 등 포함할 수 있다.
- **Lights** : 시각적으로 현실적인 환경을 만들기 위해 다양한 종류의 조명이 있으며 위치, 유형, 강도 등 설정할 수 있다.
- **Ground** : 시뮬레이션에서 floor를 설명한다. 지형, 텍스처, 색상 등 정의 할 수 있다.
- **Fluids** : 시뮬레이션 환경에서 수중환경이 필요하다면 설정할 수 있다.

- .world 파일의 예시를 보자 (주석으로 해당부분 간략히 설명)
- example.world
    
    ```jsx
    <?xml version="1.0"?>
    <sdf version="1.6">
      <world name="default">
      
    <!-- ground를 설정 -->
        <include>
          <uri>model://ground_plane</uri>  
        </include>
    <!-- 조명으로 sun을 이용 -->
        <include>
          <uri>model://sun</uri>
        </include>
    <!-- 그림자 사용 여부 -->
        <scene>
          <shadows>false</shadows>
        </scene>
    <!-- 전체화면 설정 및 실행하였을 때 사용자가 바라보는 시점 -->
        <gui fullscreen='0'>
          <camera name='user_camera'>
            <pose frame=''>0.319654 -0.235002 9.29441 0 1.5138 0.009599</pose>
            <view_controller>orbit</view_controller>
            <projection_type>perspective</projection_type>
          </camera>
        </gui>
    <!-- ODE 물리 엔진에 대한 설정 -->
        <physics type="ode">
          <real_time_update_rate>1000.0</real_time_update_rate>
          <max_step_size>0.001</max_step_size>
          <real_time_factor>1</real_time_factor>
          <ode>
            <solver>
              <type>quick</type>
              <iters>150</iters>
              <precon_iters>0</precon_iters>
              <sor>1.400000</sor>
              <use_dynamic_moi_rescaling>1</use_dynamic_moi_rescaling>
            </solver>
            <constraints>
              <cfm>0.00001</cfm>
              <erp>0.2</erp>
              <contact_max_correcting_vel>2000.000000</contact_max_correcting_vel>
              <contact_surface_layer>0.01000</contact_surface_layer>
            </constraints>
          </ode>
        </physics>
        
        <!-- 추가적인 구조물 or 로봇 등 언급 가능 (option) -->
        <!-- link 구조에 visual(시각화), Collision(충돌)에 대한 설정 -->
        <link name="wall">
          <visual name="wall">
            <geometry>
              <mesh>
                <uri>model://{world_repository}/meshes/wall.obj</uri>
              </mesh>
            </geometry>
            <transparency>0.0</transparency>
            <material>
              <diffuse>1.0 1.0 1.0</diffuse>
              <specular>0.1 0.1 0.1</specular>
              <pbr>
                <metal>
                  <metalness>0.0</metalness>
                  <albedo_map>model://{world_repository}/meshes/white_brick.png</albedo_map>
                </metal>
              </pbr>
              <script>
                <uri>model://{world_repository}/meshes/</uri>
                <name>wall_Diffuse</name>
              </script>
            </material>
          </visual>
          
          <collision name="collision">
            <geometry>
              <mesh>
                <uri>model://{world_repository}/meshes/wall.obj</uri>
              </mesh>
            </geometry>
            <surface>
              <contact>
                <collide_bitmask>0x01</collide_bitmask>
              </contact>
            </surface>
          </collision>
          <pose>-9.817441860465117 -12.710465116279071 0 0 0 0</pose>
        </link>
        
        
      </world>
    </sdf>
    ```
    

## Model

로봇, 센서 들을 정의하는 파일 형식은 SDF(Simulation Description Format), URDF(Unified Robot Description Format), Xacro 이 있다. 각각의 특징과 차이점을 파악해보자

1. **SDF** :
    - Gazebo Simulator를 위한 파일 형식으로 로봇, 센서, 환경 등 시뮬레이션 요소를 정의한다.
    - XML 형식으로 작성, 모델의 물리적 특성, 센서의 속성, 환경의 텍스처 등 정의한다.
2. **URDF** :
    - URDF는 로봇의 기하학적 및 시각적 특성을 설명한다.
    - XML 형식으로 작성, 로봇의 기하학적 형상인 로봇의 링크, 조인트를 정의하여 로봇의 구조를 설명한다.
    - 로봇 모델링 및 시각화 도구에 사용, 주로 ROS를 위한 파일 형식이다.
3. **Xacro** :
    - URDF의 확장으로 XML 기반으로 작성된 URDF 파일을 더 효율적으로 작성할 수 있다. 마찬가지로 ROS를 위한 파일 형식이다.
    - 매개변수화와 매크로 기능을 제공하여 조금 더 쉽게 코드를 재사용하고 가독성을 향상시킨다.
    - URDF 파일보다 조금 더 간결하고 유지 관리가 쉽다.

전체적인 차이는 SDF는 Gazebo Simulation을 위한 것, URDF는 로봇의 기하학적 구조를 정의하므로 로봇의 모델링과 시각화에서 ROS와 호환, Xacro는 조금 더 쉽게 URDF를 작성하는 URDF의 확장 구조이다.

다음 포스트에서 Model 파일의 구조와 Gazebo Plug-in에 대해서 알아보자.

참고 :

[Gazebo  : Tutorials](https://classic.gazebosim.org/tutorials)

