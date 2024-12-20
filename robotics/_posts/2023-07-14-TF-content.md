---
layout: post
title: TF(Transform)
description: >
  Transform Concept
sitemap: true
changefreq : daily
priority : 1.0
hide_last_modified: true
---

# TF(Transform)



![ros2_tf2_frames.png](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/tf/ros2_tf2_frames.png)

## Frame이란?

- TF에 대해서 언급하기 전에 Frame에 대한 개념이 선행되어야 한다. **Frame**은 “그 로봇의 상태가 어디에 포함되어 있는데?” 라는 것에 해답으로 로봇을 설계하고 구현하는 단계에서 로봇에서 구성된 좌표계다. 로봇의 상태, 위치를 상대적인 좌표로 표현한 것으로 이때 좌표계는 유클리드 좌표계로 구성되고 각각의 좌표계는 독립적으로 구성되어 있다.

- Frame을 예를들어 설명하면 내 오른손 검지 손톱은 어디에 있는데? 라고 하면 내 오른손 검지에 속해 있다고 볼 수 있다. 그럼 내 오른손 검지 손톱은 오른손 검지라는 Frame에 속해있고 그 위치는 대략적으로 손가락 끝이라고 볼 수 있다. 그럼 여기서 하나의 문제가 발생한다. 내 오른손 검지 손가락의 위치는 어디가 기준에서 시작되어서 오른손의 위치를 설명할 것이며 그 오른손 위치에서 또 오른손 검지 손가락의 위치, 그 다음 오른손 검지 손톱의 위치를 설명할 것이다.

- 따라서 각각의 좌표계는 분리되어 각 좌표계간의 관계를 정립해줄 필요가 있다. 절대적인 좌표계로 설명한다면 가장 기본적으로 위도와 경도에 따라서 위치를 설명할 수 있겠지만 실제로 로봇이나 좌표계에서 거리의 단위가 위도, 경도에서 설명하기에는 미시적인 개념으로 상대적인 좌표계가 필요하고 따라서 TF에 대한 개념이 필수적으로 이 문제를 해결할 수 있는 키포인트가 된다.

 

## TF는 그래서 뭐야?

- **TF**에서 좌표계에서 그럼 시작하는 좌표, 즉 기준을 삼을 좌표는 사람을 기준하면 기본적으로 **World** - **Base_Link** 로 설정할 수 있다. 로봇을 기준으로 하면 **Map** - **Base_Link**를 기준으로 설정할 수 있다. 사실 로봇은 **World** - **Map - Odom** - **Base_Link** 이지만 로봇의 움직임, 이동경로를 Map으로 한정하고 진행하는 경우가 대부분이였다. 실외 로봇, 탐사 로봇에서는 기준되는 좌표계가 World로 시작할 수 있다고 생각한다.

- 예시를 들어 map - base_link 기준으로 형성되는 TF(transform)을 간략히 설명하자면 map의 원점을 기준으로 로봇의 위치가 (2, -3, 1)만큼 떨어져 있다면 base_link 는 (0, 0, 0) 이지만 map에서의 좌표는 (2, -3, 1) 이다. 그렇다면 로봇의 양쪽에 휠이 부착되어 있고 각 휠은 base_link 기준으로 2만큼 떨어진 곳에 위치해 있다고 가정한다면 왼쪽 휠은 base_link 기준으로 (0,-2,0) 오른쪽 휠은 base_link 기준으로 (0, 2, 0)으로 이를 설명하고 표현할 수 있을것이다.

- 방금 예시에서 설명한 것처럼 map - base_link - left_wheel_link / right_wheel_link 구조로 형성되었는데 만약 로봇이 움직이면 어떻게 될까? x축방향으로 1 만큼 움직였다면 base_link의 좌표는 (1, 0, 0) 이고 map에서 해당 로봇의 좌표값은 (3, -3, 1)로 변했을 것이다.(조금 더 자세한 표현을 하자면 odom_frame이 있어야하지만 여기서는 생략하도록 하겠다.) 하지만 왼쪽 휠, 오른쪽 휠의 위치는 여전히 (0, -2, 0)이고 (0, 2, 0)이다. 왜냐하면 base_link를 기준으로 left_wheel_link 와 right_wheel_link의 좌표는 변함이 없기 때문이다. 이처럼 모든 Frame은 독립적이기 때문에 로봇이 움직임에 따라서 정적으로 변하지 않는 Frame이 있고 동적으로 변하는 Frame이 있다. 이를 **TF_static** 과 **TF_broadcaster**라고 명칭한다. 물론 listener라는 개념도 있지만 TF 에 대해서 전반적으로 이해한다면 listener에 대한 개념은 충분히 이해했다고 생각한다.

- 아래 사진은 TF_tree 개념으로 tree의 형태를 구성하고 있기때문에 사진과 같이 구성되어 있다. 여기서 나타나는 점은 frame의 parent 개념과 child개념으로 예를들면 odom_Frame 은 parent_frame으로 map을, child_frame 으로 odom_frame이 된다. 또한 여기서보면  화살표 따라서 생성된 Text에서 살펴보면 Average rate, Buffer length등 이 있는데 실시간으로 값이 변한다면 TF_broadcaster로 설계되었고 값이 변하지 않는다면 TF_static으로 설계되어 있다는 것을 확인할 수 있다.


![tf_tree.png](https://raw.githubusercontent.com/eeoon/eeoon.github.io/main/robotics/images/tf/tf_tree.png)

### TF 에 사용되는 매개변수는 다음과 같다.

- **translation** : (x,y,z) 좌표계 간의 병진운동
- **rotation** : (x,y,z,w) 좌표계 간의 회전운동
- **time** : transform을 가지는 시점
- **parent_frame** : 부모 Frame의 이름 (like, map)
- **child_frame** : 자식 Frame의 이름 (like, base_link)

**ros** 에서 사용하는 **tf2_msgs/msg** 의 형태를 먼저 찾아보면 위에서 사용된 매개변수와 동일하다.

### TF static과 TF broadcaster 에 대해서 코드를 보며 살펴보자

- 코드는 C++ 과 Python으로 작성된 예시를 아래의 참고페이지를 통해서 확인할 수 있다.

## TF_static(C++)

```jsx
#include <memory>

#include "geometry_msgs/msg/transform_stamped.hpp"
#include "rclcpp/rclcpp.hpp"
#include "tf2/LinearMath/Quaternion.h"
#include "tf2_ros/static_transform_broadcaster.h"

class StaticFramePublisher : public rclcpp::Node
{
public:
  explicit StaticFramePublisher(char * transformation[])
  : Node("static_turtle_tf2_broadcaster")
  {
    tf_static_broadcaster_ = std::make_shared<tf2_ros::StaticTransformBroadcaster>(this);

    // Publish static transforms once at startup
    this->make_transforms(transformation);
  }

private:
  void make_transforms(char * transformation[])
  {
    geometry_msgs::msg::TransformStamped t;

    t.header.stamp = this->get_clock()->now();
    t.header.frame_id = "world";
    t.child_frame_id = transformation[1];

    t.transform.translation.x = atof(transformation[2]);
    t.transform.translation.y = atof(transformation[3]);
    t.transform.translation.z = atof(transformation[4]);
    tf2::Quaternion q;
    q.setRPY(
      atof(transformation[5]),
      atof(transformation[6]),
      atof(transformation[7]));
    t.transform.rotation.x = q.x();
    t.transform.rotation.y = q.y();
    t.transform.rotation.z = q.z();
    t.transform.rotation.w = q.w();

    tf_static_broadcaster_->sendTransform(t);
  }

  std::shared_ptr<tf2_ros::StaticTransformBroadcaster> tf_static_broadcaster_;
};

int main(int argc, char * argv[])
{
  auto logger = rclcpp::get_logger("logger");

  // Obtain parameters from command line arguments
  if (argc != 8) {
    RCLCPP_INFO(
      logger, "Invalid number of parameters\nusage: "
      "$ ros2 run learning_tf2_cpp static_turtle_tf2_broadcaster "
      "child_frame_name x y z roll pitch yaw");
    return 1;
  }

  // As the parent frame of the transform is `world`, it is
  // necessary to check that the frame name passed is different
  if (strcmp(argv[1], "world") == 0) {
    RCLCPP_INFO(logger, "Your static turtle name cannot be 'world'");
    return 1;
  }

  // Pass parameters and initialize node
  rclcpp::init(argc, argv);
  rclcpp::spin(std::make_shared<StaticFramePublisher>(argv));
  rclcpp::shutdown();
  return 0;
}
```


- 코드에서 간략하게 살펴보면 time / header / translation / rotation 을 각각 input 해주고 있고 rotation 에서는 quaternion 에 대한 값으로 정의하고 있다.

- 다음으로 TF_broadcaster에 대한 코드를 살펴보자

## TF_broadcaster(C++)

```jsx
#include <functional>
#include <memory>
#include <sstream>
#include <string>

#include "geometry_msgs/msg/transform_stamped.hpp"
#include "rclcpp/rclcpp.hpp"
#include "tf2/LinearMath/Quaternion.h"
#include "tf2_ros/transform_broadcaster.h"
#include "turtlesim/msg/pose.hpp"

class FramePublisher : public rclcpp::Node
{
public:
  FramePublisher()
  : Node("turtle_tf2_frame_publisher")
  {
    // Declare and acquire `turtlename` parameter
    turtlename_ = this->declare_parameter<std::string>("turtlename", "turtle");

    // Initialize the transform broadcaster
    tf_broadcaster_ =
      std::make_unique<tf2_ros::TransformBroadcaster>(*this);

    // Subscribe to a turtle{1}{2}/pose topic and call handle_turtle_pose
    // callback function on each message
    std::ostringstream stream;
    stream << "/" << turtlename_.c_str() << "/pose";
    std::string topic_name = stream.str();

    subscription_ = this->create_subscription<turtlesim::msg::Pose>(
      topic_name, 10,
      std::bind(&FramePublisher::handle_turtle_pose, this, std::placeholders::_1));
  }

private:
  void handle_turtle_pose(const std::shared_ptr<turtlesim::msg::Pose> msg)
  {
    geometry_msgs::msg::TransformStamped t;

    // Read message content and assign it to
    // corresponding tf variables
    t.header.stamp = this->get_clock()->now();
    t.header.frame_id = "world";
    t.child_frame_id = turtlename_.c_str();

    // Turtle only exists in 2D, thus we get x and y translation
    // coordinates from the message and set the z coordinate to 0
    t.transform.translation.x = msg->x;
    t.transform.translation.y = msg->y;
    t.transform.translation.z = 0.0;

    // For the same reason, turtle can only rotate around one axis
    // and this why we set rotation in x and y to 0 and obtain
    // rotation in z axis from the message
    tf2::Quaternion q;
    q.setRPY(0, 0, msg->theta);
    t.transform.rotation.x = q.x();
    t.transform.rotation.y = q.y();
    t.transform.rotation.z = q.z();
    t.transform.rotation.w = q.w();

    // Send the transformation
    tf_broadcaster_->sendTransform(t);
  }

  rclcpp::Subscription<turtlesim::msg::Pose>::SharedPtr subscription_;
  std::unique_ptr<tf2_ros::TransformBroadcaster> tf_broadcaster_;
  std::string turtlename_;
};

int main(int argc, char * argv[])
{
  rclcpp::init(argc, argv);
  rclcpp::spin(std::make_shared<FramePublisher>());
  rclcpp::shutdown();
  return 0;
}
```

# 결론

전반적으로 frame의 개념을 이해한 것을 바탕으로 TF에 대해서 이해하고 이를 실제 로봇에 적용시킬 수 있어야한다. 따라서 TF_tree 를 살펴보는 법, 코드에서 의미하는 바 등을 간략하게 살펴보았다.

간단하게 정리하자면 다음과 같다.

- Static 과 Broadcaster에 대한 차이는 Static으로 tf를 정의하면 고정된 좌표에서 상대적인 좌표를 정의하기 때문에 정적인 움직임에 대해서 실시간으로 변하는 동적 움직임을 정의할 수 없다. 따라서 Broadcaster의 방법으로 tf를 정의하면 실시간으로 움직이는 프레임에 대해서 상대적인 좌표를 정의하기 때문에 움직이는 로봇에서 상대적인 좌표(Map) 을 정의하기에 유용하다. 따라서 Odometry를 계산할 때 로봇의 위치가 map 기준으로 실시간으로 변하는데 이를 Broadcaster 방식으로 Odometry를 정의하기 때문에 그렇다.

- 물론 로봇의 모든 frame을 broadcaster 하지 말아야한다. 왜냐하면 로봇의 Base_link로 부터 로봇의 wheel_link 는 정적(Static)인 좌표로 절대 변하지 않기 때문에 이런 부분은 Static으로 tf를 정의할 수 있다. 따라서 모든 부분이 정적으로, 동적으로 상대 좌표를 정의하는 것이 아닌 상황에 맞도록 동적인 것은 Broadcaster로, 정적인 것은 Static으로 정의할 수 있다.

참고

[Writing a broadcaster (C++) — ROS 2 Documentation: Humble  documentation](https://docs.ros.org/en/humble/Tutorials/Intermediate/Tf2/Writing-A-Tf2-Broadcaster-Cpp.html)

[tf - ROS Wiki](http://wiki.ros.org/tf)

[tf/Tutorials - ROS Wiki](https://wiki.ros.org/tf/Tutorials)

[tf2/Tutorials/Quaternions - ROS Wiki](http://wiki.ros.org/tf2/Tutorials/Quaternions)
