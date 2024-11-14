# Gazebo Plugin_Battery

- Gazebo Plugin 에 대한 기본적인 개념은 이전 페이지에서 언급했다. 참고 : [https://eeoon.github.io/robotics/Gazebo_Plugin-content/](https://eeoon.github.io/robotics/Gazebo_Plugin-content/)

- 배터리 시스템을 Gazebo Plugin을 통해서 Custom 하는 간단한 예시를 진행하면서 Gazebo Plugin에 대해서 조금 더 깊게 알아보자.

### 배터리 시스템

- 배터리 시스템은 다양하게 구현될 수 있겠지만 기본적으로 대기전력, 소비전력만 고려하여 구현한다. (사실은 BMS(Battery Management System)에서는 비선형으로 전력소모를 고려하겠지만..)
- 대기전력 시스템을 고려하여 로봇이 움직이지 않더라도 배터리 소모가 어느정도 선형적으로 발생하는 것을 기본으로 로봇이 움직이는 경우에는 모터 드라이버의 전력 소모까지 고려하여 조금 더 큰 폭으로 배터리 소모가 진행될 수 있도록 하였다.
- 사진 첨부
    
    그래프 100 퍼센트에서 경사비탈길 같은 배터리 소모 그래프
    

### Gazebo Plugin (Custom)

- Gazebo Plugin을 커스텀하기 위해서는 다음과 같이 진행한다.

1. Workspace에서 source file을 생성하고 Gazebo에 대한 해더파일을 참고할 수 있도록 한다.
    
    ```jsx
    #include <gazebo/common/Plugin.hh>
    #include <rclcpp/rclcpp.hpp>
    #include <std_msgs/msg/float32.hpp>
    #include <std_msgs/msg/string.hpp>
    #include <gazebo_ros/node.hpp>
    #include <gazebo/physics/Model.hh>
    #include <gazebo/physics/World.hh>
    #include <gazebo/physics/Link.hh>
    #include <gazebo/physics/Joint.hh>
    #include <geometry_msgs/msg/twist.hpp>
    #include <gazebo/common/common.hh>
    #include <gazebo/common/Exception.hh>
    #include <gazebo/transport/transport.hh>
    
    // 이외 필요한 해더파일을 추가한다.
    ```
    
2. SDF 파일에서 parameter를 설정할 수 있도록 하기 위해서 다음과 같이 설정한다.
    
    ```jsx
    // Read parameters from SDF
    if (_sdf->HasElement("topic_name"))
      topic_name_ = _sdf->Get<std::string>("topic_name");
    else
      topic_name_ = "battery_status";  // default value
    ```
    
3. 배터리 정보의 토픽을 publish 하기 위한 부분 설정한다.
    
    ```jsx
    battery_pub_ = node_->create_publisher<sensor_msgs::msg::BatteryState>(topic_name_, 10);
    ```
    
4. 배터리 상태를 업데이트하는 callback 함수를 생성해서 구현한다.
    
    ```jsx
    battery_charge_ -= scale_ * power_consumption * dt / 3600.0;  // Convert power (W) to energy (Ah)
    voltage_ = (battery_charge_ / capacity_) * constant_voltage_;
    double battery_percentage = (battery_charge_ / capacity_) * 100.0;
    ```
    
5. CMakeLists.txt 파일에서 Class 를 library에 추가하고 이외의 설정을 진행한다.
    
    ```jsx
    cmake_minimum_required(VERSION 3.5)
    project(gazebo_battery_plugin)
    
    find_package(gazebo_ros REQUIRED)
    # ... 이외의 find_package 설정
    # ... ex, find_package(rclcpp REQUIRED)
    
    # include directories(
    include
    ${GAZEBO_INCLUDE_DIRS}
    ${rclcpp_INCLUDE_DIRS}
    # ... 이외의 include 설정
    
    # Build the plugin
    add_library(BatteryPlugin SHARED src/BatteryPlugin.cc)
    ament_target_dependencies(BatteryPlugin rclcpp std_msgs gazebo_ros geometry_msgs gazebo_dev sensor_msgs)
    target_link_libraries(BatteryPlugin ${GAZEBO_LIBRARIES})
    
    # install
    install(TARGETS BatteryPlugin
    LIBRARY DESTINATION lib
    )
    
    #ament_package()
    ```
    
6. build 폴더를 생성하고 cmake를 진행해서 build 파일에 plugin에 대한 lib를 생성한다
    
    `cd build`
    
    `cmake ..`
    
    `make`
    
7. 생성된 lib를 Gazebo 에서 plugin 할수 있도록 build 폴더를 참조할 수 있도록 한다.
    
    `export GAZEBO_PLUGIN_PATH=$HOME/gazebo_battery_plugin/build:$GAZEBO_PLUGIN_PATH` 
    
8. 해당 plugin을 사용하기 위해서 이전에 다른 센서들의 plugin을 추가한 것 처럼 SDF 파일에서 설정한다.
    
    ```jsx
     <!-- *********************** Battery STATE ***************************    -->
    
        <plugin name="BatteryPlugin" filename="libBatteryPlugin.so">
          <topic_name>battery_state</topic_name>
          <battery_charge>2.5</battery_charge>
          <scale>100.0</scale>
          <capacity>2.5</capacity>
          <voltage>39.6</voltage>
          <constant_voltage>39.6</constant_voltage>
          <lin_discharge_coeff>-1.0</lin_discharge_coeff>
          <design_capacity>3.3</design_capacity>
        </plugin>
    ```
    

1. 토픽 데이터 확인
    - **topic : battery_state / battery_percentage**
    - battery_state 에서는 전압, 전류 등 sensor_msg 에 해당하는 데이터를 , battery_percentage는 battery_state 중, 배터리 잔량 표시를 출력한다.
    
    ![image.png](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/gazebo_battery_plugin/image.png)
    

참고 링크 : https://classic.gazebosim.org/tutorials?cat=guided_i&tut=guided_i5