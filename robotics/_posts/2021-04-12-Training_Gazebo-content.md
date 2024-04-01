---
layout: post
title: Training_Gazebo
description: >
  Training_Gazebo
sitemap: true
changefreq : daily
priority : 1.0
hide_last_modified: true
---

# Training Gazebo

## Gazebo 실습

- turtlebot3_burger와 3D_Lidar(velodyne)으로 구성된 로봇을 Gazebo 환경에서 불러와 Lidar 데이터를 확인하고 간단한 조작을 진행한다.
- Gazebo 실습과 함께 rviz에 대한 간략한 실행 그리고 tf_tree에 대한 것도 함께 구성하였다.

### Gazebo 실습 환경

- ROS2 (humble)
- Gazebo 11
- Turtlebot3_world

### Gazebo Package를 설치

```
sudo apt-get update

sudo apt install -y ros-humble-gazebo-ros

sudo apt install -y ros-humble-gazebo-ros-pkgs
```

### Turtlebot3_world package 설치

turtlebot3_burger with velodyne으로 환경 구성된 turtlebot3_world로 테스트 환경

[https://github.com/eeoon/turtlebot3_velodyne_Gazebo](https://github.com/eeoon/turtlebot3_velodyne_Gazebo)

**Installation**

```python
source /opt/ros/humble/setup.bash
sudo apt-get update
mkdir -p eon_ws/src
cd ~/eon_ws/src
git clone https://github.com/eeoon/turtlebot3_velodyne_Gazebo.git
cd ..
colcon build
source install/setup.bash
export TURTLEBOT3_MODEL=burger
. /usr/share/gazebo/setup.sh
```

**Run**

```python
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py

ros2 run teleop_twist_keyboard teleop_twist_keyboard 
```

## 실행결과

- 정상적으로 실행 되었다면 다음과 같은 화면을 볼 수 있다. 오른쪽화면이 Gazebo 환경으로 Turtlebot3_World에서 Turtlebot3_burger의 머리에 velodyne을 장착하고 나타난 것을 확인할 수 있다.
- 좌측화면은 rviz2 (ros1에서는 rviz) 화면으로 rviz2는 데이터를 시각화 하기 위해 사용되는 ros의 기본적인 툴로 좌측 상단에 `fixed_frame`을 `base_scan`으로 변경하면 해당 데이터를 visualize할 수 있다.

![result_picture.png](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/training_gazebo/image.png)


- TF_tree를 살펴봤을 때 다음과 같이 나타난다.
- 간단하게 로봇의 링크 구조를 나타내며 전체적인 구조를 나타낸다.
- TF에 대해서는 추후 Transform(TF)에서 포스터 작성 예정

![rqt_tf_tree.png](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/training_gazebo/image2.png)

참고 :

[turtlebot3_velodyne_gazebo github](https://github.com/eeoon/turtlebot3_velodyne_Gazebo)
