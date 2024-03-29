---
layout: post
title: Gazebo_Plugin
description: >
  Gazebo_Plugin(Sensor, Actuator)
sitemap: true
changefreq : daily
priority : 1.0
hide_last_modified: true
---

# Gazebo-Plugin

![Untitled](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/gazebo_plugin/image.png)

- Gazebo post에 이어서 Gazebo-Plugin에 대해서 알아보자.
- Gazebo 패키지가 ros1을 기반으로 ros2로 release되면서 추가되거나 변경 사항이 생겨 ros1과 ros2에 대해 각각 xml의 형태가 다를 수 있다. 여기서는 ros2(humble)을 기준으로 작성하였다.
- Gazebo-plugin은 simulation 환경에서 여러 기능을 제공한다. 예를 들어 센서 계측, 액츄에이터 제어 등 이를 통해 외부 프로그램을 통해 Gazebo 환경에서 데이터를 주고받으며 실제 환경처럼 모사할 수 있게된다.
- 그 중에서 Sensor, Actuator Plugin에 대해서 깊게 분석해보자. (자주 사용되는 plugin)

## Sensor-Plugin

- Sensor의 종류가 다양하기 때문에 각각의 Sensor로 부터 Sensing되는 데이터를 계측하고 이를 사용자 또는 다른 프로그램과 연동하여 진행할 수 있다.
- 따라서 먼저 Gazebo에서 Sensor를 종류별로 분리하면 다음과 같다.(각 센서의 특징은 검색해보길 바란다.)

1. **Lidar(2D, 3D)**
2. **IMU**
3. **Camera(RGB, Depth)**
4. **GPS**
5. **Bumper**

- 먼저 Lidar Sensor를 사용하기 위해 사용되는 plugin은 libgazebo_ros_ray_sensor이다. 파일 확장자를 보면 .so 파일은 Windows 에서 .dll 파일로 Linux에서는 .so파일을 사용하는데 해당 파일은 동적 라이브러리로 프로그램 실행 중에 특정 library를 사용하고 싶을 때 로드한다.

### Lidar_Sensor

```jsx
<!-- 2D Lidar는 다음과 같다.-->
<plugin name="example_laserscan" filename="libgazebo_ros_ray_sensor.so">
  <!-- ros와 연동하기 위해서 사용되는 부분으로 scan data를 출력한다. 또한 namespace는 필수가 아니기 때문에 주석처리할 수 있다. -->
  <ros>
    <namespace>example_lidar</namespace>
    <remapping>~/out:=scan</remapping>
  </ros>
  <!-- 출력 형태(msg)는 sensor_msgs/LaserScan의 구조로 구성되며 Lidar Sensor의 frame을 설정한다. -->
  <output_type>sensor_msgs/LaserScan</output_type>
  <frame_name>example_lidar_link</frame_name>
</plugin>

<!-- 3D Lidar(Velodyne)는 다음과 같다. -->

<plugin name="gazebo_ros_laser_controller" filename="libgazebo_ros_velodyne_laser.so">
  <ros>
    <namespace>/example_3dlidar</namespace>
    <remapping>~/out:=example_points_cloud</remapping>
  </ros>
  <topicName>velodyne_points</topicName>
  <frameName>base_scan</frameName>
  <organize_cloud>${true of false}</organize_cloud>
  <min_range>0.9</min_range>
  <max_range>130</max_range>
  <gaussian_noise>0.008</gaussian_noise>
</plugin>
        
```

### **IMU**

```jsx
 <!-- IMU는 다음과 같다.-->

<plugin name="example_imu" filename="libgazebo_ros_imu_sensor.so">
    <!-- ros와 연동하기 위해서 사용되는 부분으로 IMU data를 출력한다 -->      
    <ros>
      <namespace>imu</namespace>
      <remapping>~/out:=data</remapping>
    </ros>
    <initial_orientation_as_reference>false</initial_orientation_as_reference>
</plugin>
```

### **Camera(RGB, Depth)**

```jsx
       <!-- Camera는 RGB로 사용할 것인지, Depth에 대한 정보도 포함할 것인지 구분할 수 있다.-->

        <plugin name="example_camera" filename="libgazebo_ros_camera.so">
            <ros>
                <namespace>example_camera</namespace>
                <remapping>~/image_raw:=image_raw</remapping>
                <remapping>~/camera_info:=camera_info</remapping>
                <!-- Depth Camera 설정 부분 (Option) ${name}으로 된 부분은 위의 namespace에서 설정한 example_camera가 삽입된다.-->
                <remapping>${name}/depth/image_raw:=depth/image_rect_raw</remapping>
                <remapping>${name}/depth/camera_info:=depth/camera_info</remapping>
                <remapping>${name}/points:=depth/points</remapping>
            </ros>
            <camera_name>${name}</camera_name>
            <frame_name>${name}_link_optical</frame_name>
            <hack_baseline>0.2</hack_baseline>
            <!-- Depth Camera 설정 부분 (Option) -->
            <min_depth>0.05</min_depth>
            <max_depth>8.0</max_depth>            
        </plugin>        
```

### **GPS**

```jsx
<!-- GPS는 다음과 같다.-->

<plugin name="example_gps" filename="libgazebo_ros_gps_sensor.so">
    <ros>
      <namespace>/example_gps</namespace>
      <remapping>~/out:=gps</remapping>
    </ros>
    <frame_name>example_gps_link</frame_name>
</plugin>
```

### **Bumper**

```jsx
<!-- Bumper_sensor는 다음과 같다.-->

<plugin name="exmaple_bumper" filename="libgazebo_ros_bumper.so">
    <ros>
      <namespace>example_bumper</namespace>
      <remapping>bumper_states:=bumper_demo</remapping>
    </ros>
    <frame_name>example_bumper_link</frame_name>
</plugin>
```

## Actuator-Plugin

- Actuator에 대한 Plugin은 구동부와 Joint에 대한 제어를 담당한다.

### Joint_State_Publisher

```jsx
<!-- Joint_State_Publisher는 다음과 같다.-->

<plugin name="example_joint_state" filename="libgazebo_ros_joint_state_publisher.so">
  <ros>
    <namespace>/example</namespace>
    <remapping>~/out:=joint_states</remapping>
  </ros>
  <update_rate>30</update_rate>
  <joint_name>left_wheel_joint</joint_name>
  <joint_name>right_wheel_joint</joint_name>
</plugin>
```

### Differential_Drive_Controller

```jsx
<!-- Differential Drive Controller는 다음과 같다.-->

<plugin name="example_diff_drive" filename="libgazebo_ros_diff_drive.so">

  <ros>
    <namespace>/example</namespace>
  </ros>

  <update_rate>30</update_rate>

  <!-- wheels -->
  <left_joint>left_wheel_joint</left_joint>
  <right_joint>right_wheel_joint</right_joint>

  <!-- kinematics -->
  <wheel_separation>${base_length + 2*wheel_base_separation}</wheel_separation>
  <wheel_diameter>${2*wheel_radius}</wheel_diameter>

  <!-- limits -->
  <max_wheel_torque>20</max_wheel_torque>
  <max_wheel_acceleration>1.0</max_wheel_acceleration>

  <command_topic>cmd_vel</command_topic>

  <!-- output -->
  <publish_odom>true</publish_odom>
  <publish_odom_tf>true</publish_odom_tf>
  <publish_wheel_tf>false</publish_wheel_tf>

  <odometry_topic>odom</odometry_topic>
  <odometry_frame>odom</odometry_frame>
  <robot_base_frame>base_link</robot_base_frame>

</plugin>
```

# 결론

- 가장 대표적인 센서, 구동부를 Gazebo 시뮬레이션에서 어떻게 plugin하는지 살펴봤다.
- plugin에 link, joint 구조가 존재하여야하고 센서에서는 noise 처리, visualization, range, distortion 등 설정할 부분이 많다. 이 부분은 gazebo_plugin_tutorial을 참고하길 바란다.
- 각 센서, 구동부를 정의하면 ros2에서 사용하는 `ros2 topic list` 에서 관측되는 Data를 확인할 수 있다.
- 다음과정에서 실제 Gazebo 환경에서 구동되는 센서, 모터를 확인한다.

참고 : 

[A Review of the ROS2 URDF Gazebo Sensor](https://medium.com/@bytesrobotics/a-review-of-the-ros2-urdf-gazebo-sensor-91e947c633d7)

[[ROS2 Q&A] 240 - How to use Gazebo plugins in ROS 2](https://www.youtube.com/watch?v=JJDebiniDBw)
