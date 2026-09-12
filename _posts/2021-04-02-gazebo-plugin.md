---
layout: post
title: "Gazebo Plugins: Sensor and Actuator Plugin Analysis"
date: 2021-04-02 09:00:00 +0900
description: "XML structure and usage of sensor and actuator plugins in Gazebo (ROS2 Humble)"
tags: [gazebo, simulation, ros2]
categories: tech-note
giscus_comments: false
related_posts: false
---

![Untitled](/assets/img/blog/robotics/gazebo_plugin/image.png)

- Following the Gazebo post, this post looks at Gazebo plugins.
- As the Gazebo packages were released for ROS2 based on the ROS1 versions, additions and changes were made, so the XML form may differ between ROS1 and ROS2. This post is written for ROS2 (Humble).
- Gazebo plugins provide a range of functionality in the simulation environment, such as sensor measurement and actuator control. Through them, external programs can exchange data with the Gazebo environment and emulate the real environment.
- Among these, the sensor and actuator plugins (the most frequently used ones) are analyzed in depth here.

## Sensor-Plugin

- Since there are many kinds of sensors, the data sensed by each sensor can be measured and connected to the user or to other programs.
- The sensors available in Gazebo can be grouped as follows. (Please look up the characteristics of each sensor separately.)

1. **Lidar(2D, 3D)**
2. **IMU**
3. **Camera(RGB, Depth)**
4. **GPS**
5. **Bumper**

- The plugin used for the Lidar sensor is `libgazebo_ros_ray_sensor`. Regarding the file extension: Linux uses .so files where Windows uses .dll files. These are dynamic libraries that are loaded when a particular library is needed while a program is running.

### Lidar_Sensor

```jsx
<!-- 2D LiDAR is configured as follows -->
<plugin name="example_laserscan" filename="libgazebo_ros_ray_sensor.so">
  <!-- ROS integration section that publishes the scan data; the namespace is optional and can be commented out -->
  <ros>
    <namespace>example_lidar</namespace>
    <remapping>~/out:=scan</remapping>
  </ros>
  <!-- the output message follows sensor_msgs/LaserScan and sets the frame of the LiDAR sensor -->
  <output_type>sensor_msgs/LaserScan</output_type>
  <frame_name>example_lidar_link</frame_name>
</plugin>

<!-- 3D LiDAR (Velodyne) is configured as follows -->

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
 <!-- IMU is configured as follows -->

<plugin name="example_imu" filename="libgazebo_ros_imu_sensor.so">
    <!-- ROS integration section that publishes the IMU data -->      
    <ros>
      <namespace>imu</namespace>
      <remapping>~/out:=data</remapping>
    </ros>
    <initial_orientation_as_reference>false</initial_orientation_as_reference>
</plugin>
```

### **Camera(RGB, Depth)**

```jsx
<!-- the camera can be used as RGB only or can also include depth information -->

<plugin name="example_camera" filename="libgazebo_ros_camera.so">
    <ros>
        <namespace>example_camera</namespace>
        <remapping>~/image_raw:=image_raw</remapping>
        <remapping>~/camera_info:=camera_info</remapping>
        <!-- depth camera settings (optional); ${name} is replaced by example_camera set in the namespace above -->
        <remapping>${name}/depth/image_raw:=depth/image_rect_raw</remapping>
        <remapping>${name}/depth/camera_info:=depth/camera_info</remapping>
        <remapping>${name}/points:=depth/points</remapping>
    </ros>
    <camera_name>${name}</camera_name>
    <frame_name>${name}_link_optical</frame_name>
    <hack_baseline>0.2</hack_baseline>
    <!-- depth camera settings (optional) -->
    <min_depth>0.05</min_depth>
    <max_depth>8.0</max_depth>            
</plugin>        
```

### **GPS**

```jsx
<!-- GPS is configured as follows -->

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
<!-- the bumper sensor is configured as follows -->

<plugin name="exmaple_bumper" filename="libgazebo_ros_bumper.so">
    <ros>
      <namespace>example_bumper</namespace>
      <remapping>bumper_states:=bumper_demo</remapping>
    </ros>
    <frame_name>example_bumper_link</frame_name>
</plugin>
```

## Actuator-Plugin

- Actuator plugins are responsible for controlling the drive units and joints.

### Joint_State_Publisher

```jsx
<!-- Joint_State_Publisher is configured as follows -->

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
<!-- the differential drive controller is configured as follows -->

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

# Conclusion

- This post looked at how the most representative sensors and drive units are added as plugins in Gazebo simulation.
- A plugin requires a link and joint structure, and sensors have many settings such as noise handling, visualization, range, and distortion. Refer to the gazebo_plugin_tutorial for these.
- Once each sensor and drive unit is defined, the resulting data can be observed with `ros2 topic list` in ROS2.
- The next post verifies the sensors and motors running in an actual Gazebo environment.

References : 

[Gazebo  : Tutorial : Gazebo plugins in ROS](https://classic.gazebosim.org/tutorials?tut=ros_gzplugins)

[A Review of the ROS2 URDF Gazebo Sensor](https://medium.com/@bytesrobotics/a-review-of-the-ros2-urdf-gazebo-sensor-91e947c633d7)

[[ROS2 Q&A] 240 - How to use Gazebo plugins in ROS 2](https://www.youtube.com/watch?v=JJDebiniDBw)
