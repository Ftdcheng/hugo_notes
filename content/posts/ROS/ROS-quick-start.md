---
title: "ROS"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["ROS"]
---

# ROS

## 基本概念

### 节点

ROS包中的可执行文件，可以发布话题，订阅话题，发布服务，请求服务。

查看当前正在运行的节点

```bash
rosnode list
```

查看节点的相关信息，包括发布了什么话题，订阅了什么话题，发布了什么服务

```bash
rosnode info <节点名>
```

运行节点

```bash
rosrun <包名> <节点名>
```

测试节点连通性和延迟

```bash
rosnode ping <节点名>
```

### 客户端库

ROS客户端库允许节点使用不同的语言来进行交流：

* rospy = python客户端库
* roscpp = c++ 客户端库

### roscore

你要使用ROS，就得先运行roscore。

就是主节点(Master) + rosout + 参数服务器

## 话题

查看话题已经发布的消息：

```bash
rostopic echo <话题>
```

查看发布特定话题的节点和接收的节点

```bash
rostopic list <话题>
```

查看话题消息的类型

```bash
rostopic type <话题>
```

查看话题消息的结构

```bash
rostopic show <话题>
```

发布消息

```bash
rostopic pub <话题> <类型> <参数>
```

查看话题发布的频率

```bash
rostopic hz <话题>
```

话题的时间坐标图像

```bash
rqt_plot
```

## 服务

列出活跃的服务

```bash
rosservice list
```

查看服务响应的类型

```bash
rosservice type <服务>
```

查看服务的结构

```bash
rossrv show <类型>
```

## 参数

列出可用的参数

```bash
rosparam list
```

获取参数

```bash
rosparam get <参数名>
```

获取整个参数服务器的所有参数

```bash
rosparam get /
```

设置参数，rosparam使用yaml格式

```bash
rosparam set <参数名> <参数值>
```

导出参数为yaml格式

```bash
rosparam dump <文件名> <命名空间>
```

加载yaml格式的参数

```bash
rosparam load <文件名> <命名空间>
```

## 日志

可视化显示各级日志，可以进行按等级等筛选

```bash
rosrun 	rqt_console rqt_console
```

## roslaunch

启动launch文件规定的一系列节点

```bash
roslaunch <包名> <launch文件名>
```

## 信息格式

### msg

定义话题消息的字段的文本文件，这个文件会被转换成不同的语言

这个文件放在包目录的msg目录下

如果要使用这一文件，还需要在编译配置文件package.xml中加上

```cmake
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
```

还要在CMakeLists.txt中加上三方库依赖，需要在find_package中加入msg_generation

```cmake
find_package(catkin REQUIRED COMPONENTS
	roscpp
	rospy
	std_msgs
	message_generation
)
```

再导出消息运行时依赖

```cmake
catkin_package(
...
CATKIN_DEPENDS message_runtime
...
)
```

再加上消息文件的编译选项

```cmake
add_message_files(
	FILES
	Num.msg
)
```

再加上生成消息的依赖

```cmake
generate_messages(
	DEPENDENCIES
	std_msgs
)
```

### srv

定义服务请求和响应的字段，这个文件会被转换成不同的语言

这个文件放在包目录的srv目录下

srv分为两部分，第一部分是请求，第二部分是响应，中间用---隔开

如果要使用这一文件，还需要在编译配置文件packages.xml中加上

```cmake
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
```

还要在CMakeLists.txt中加上三方库依赖，需要在find_package中加入msg_generation

```cmake
find_package(catkin REQUIRED COMPONENTS
	roscpp
	rospy
	std_msgs
	message_generation
)
```

再导出消息运行时依赖

```cmake
catkin_package(
...
CATKIN_DEPENDS message_runtime
...
)
```

再加上服务文件的编译选项

```cmake
add_service_files(
	FILES
	srvs.srv
)
```

再加上生成消息的依赖

```cmake
generate_messages(
	DEPENDENCIES
	std_msgs
)
```

### 基本类型

整数：int8, int16, int32, int64

浮点数：float32, float64

字符串：string

时间，时长

其它msg文件

数组（变长的和定长的）

Header（含有时间戳和坐标帧信息）

## 发布者和订阅者代码(C++)

### 发布者

```c++
#include "ros/ros.h"
#include "std_msgs/String.h"

#include <sstream>

int main(int argc, char **argv)
{
  /**
   * The ros::init() function needs to see argc and argv so that it can perform
   * any ROS arguments and name remapping that were provided at the command line.
   * For programmatic remappings you can use a different version of init() which takes
   * remappings directly, but for most command-line programs, passing argc and argv is
   * the easiest way to do it.  The third argument to init() is the name of the node.
   *
   * You must call one of the versions of ros::init() before using any other
   * part of the ROS system.
   */
  ros::init(argc, argv, "talker");

  /**
   * NodeHandle is the main access point to communications with the ROS system.
   * The first NodeHandle constructed will fully initialize this node, and the last
   * NodeHandle destructed will close down the node.
   */
  ros::NodeHandle n;

  /**
   * The advertise() function is how you tell ROS that you want to
   * publish on a given topic name. This invokes a call to the ROS
   * master node, which keeps a registry of who is publishing and who
   * is subscribing. After this advertise() call is made, the master
   * node will notify anyone who is trying to subscribe to this topic name,
   * and they will in turn negotiate a peer-to-peer connection with this
   * node.  advertise() returns a Publisher object which allows you to
   * publish messages on that topic through a call to publish().  Once
   * all copies of the returned Publisher object are destroyed, the topic
   * will be automatically unadvertised.
   *
   * The second parameter to advertise() is the size of the message queue
   * used for publishing messages.  If messages are published more quickly
   * than we can send them, the number here specifies how many messages to
   * buffer up before throwing some away.
   */
  ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);

  ros::Rate loop_rate(10);

  /**
   * A count of how many messages we have sent. This is used to create
   * a unique string for each message.
   */
  int count = 0;
  while (ros::ok())
  {
    /**
     * This is a message object. You stuff it with data, and then publish it.
     */
    std_msgs::String msg;

    std::stringstream ss;
    ss << "hello world " << count;
    msg.data = ss.str();

    ROS_INFO("%s", msg.data.c_str());

    /**
     * The publish() function is how you send messages. The parameter
     * is the message object. The type of this object must agree with the type
     * given as a template parameter to the advertise<>() call, as was done
     * in the constructor above.
     */
    chatter_pub.publish(msg);

    ros::spinOnce();

    loop_rate.sleep();
    ++count;
  }


  return 0;
}
```

ros::Rate可以让ros::sleep()每次休眠正确的时间，以控制循环执行的频率

ros::ok()在这些情况下会返回false:

* Ctrl + C
* 被同名节点踢出网络
* 应用的其它部分调用了ros::shutdown()
* 所有的ros::NodeHandle都被销毁了

ros::NodeHandle可以看作是节点的替身，你可以使用NodeHandle对节点进行操作，比如发布消息等，所以NodeHandle的销毁就是节点的销毁

ros::init()是为了方便命令行进行重映射而写的

使用NodeHandle.advertise后会返回一个Publisher，你可以使用它发布消息

ROS_INFO向rosout中发布INFO等级的日志

ros::spinOnce()会从全局回调函数队列中拿一个可以执行的回调函数进行执行。这个程序中没有订阅话题，没有回调函数，所以并不会执行回调函数。如果在一个有回调函数的程序没有写任何和spinOnce类似的代码，那么你写的回调函数永远都不会执行。

ros::sleep()让节点休眠正确的时间，让循环控制在10Hz的频率。

### 订阅者

```c++
#include "ros/ros.h"
#include "std_msgs/String.h"

/**
 * This tutorial demonstrates simple receipt of messages over the ROS system.
 */
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
  ROS_INFO("I heard: [%s]", msg->data.c_str());
}

int main(int argc, char **argv)
{
  /**
   * The ros::init() function needs to see argc and argv so that it can perform
   * any ROS arguments and name remapping that were provided at the command line.
   * For programmatic remappings you can use a different version of init() which takes
   * remappings directly, but for most command-line programs, passing argc and argv is
   * the easiest way to do it.  The third argument to init() is the name of the node.
   *
   * You must call one of the versions of ros::init() before using any other
   * part of the ROS system.
   */
  ros::init(argc, argv, "listener");

  /**
   * NodeHandle is the main access point to communications with the ROS system.
   * The first NodeHandle constructed will fully initialize this node, and the last
   * NodeHandle destructed will close down the node.
   */
  ros::NodeHandle n;

  /**
   * The subscribe() call is how you tell ROS that you want to receive messages
   * on a given topic.  This invokes a call to the ROS
   * master node, which keeps a registry of who is publishing and who
   * is subscribing.  Messages are passed to a callback function, here
   * called chatterCallback.  subscribe() returns a Subscriber object that you
   * must hold on to until you want to unsubscribe.  When all copies of the Subscriber
   * object go out of scope, this callback will automatically be unsubscribed from
   * this topic.
   *
   * The second parameter to the subscribe() function is the size of the message
   * queue.  If messages are arriving faster than they are being processed, this
   * is the number of messages that will be buffered up before beginning to throw
   * away the oldest ones.
   */
  ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);

  /**
   * ros::spin() will enter a loop, pumping callbacks.  With this version, all
   * callbacks will be called from within this thread (the main one).  ros::spin()
   * will exit when Ctrl-C is pressed, or the node is shutdown by the master.
   */
  ros::spin();

  return 0;
}
```

ros::spin()会等当前线程中的所有回调函数执行才返回值。

## 服务端和客户端代码（C++）

### 服务端

```c++
#include "ros/ros.h"
#include "beginner_tutorials/AddTwoInts.h"

/**
* 处理客户端请求的回调函数，一个参数是请求，一个参数是响应。
* 请求会在客户端请求的时候给服务端，也就是说在这个程序中是输入。
* 响应会根据请求生成，最后发送给客户端，在这个程序中是输出。
*/
bool add(beginner_tutorials::AddTwoInts::Request  &req,
         beginner_tutorials::AddTwoInts::Response &res)
{
  res.sum = req.a + req.b;
  ROS_INFO("request: x=%ld, y=%ld", (long int)req.a, (long int)req.b);
  ROS_INFO("sending back response: [%ld]", (long int)res.sum);
  return true;
}

int main(int argc, char **argv)
{
  ros::init(argc, argv, "add_two_ints_server");
  ros::NodeHandle n;

  ros::ServiceServer service = n.advertiseService("add_two_ints", add);
  ROS_INFO("Ready to add two ints.");
  ros::spin();

  return 0;
}
```

### 客户端

```c++
#include "ros/ros.h"
#include "beginner_tutorials/AddTwoInts.h"
#include <cstdlib>

int main(int argc, char **argv)
{
  ros::init(argc, argv, "add_two_ints_client");
  if (argc != 3)
  {
    ROS_INFO("usage: add_two_ints_client X Y");
    return 1;
  }

  ros::NodeHandle n;
  ros::ServiceClient client = n.serviceClient<beginner_tutorials::AddTwoInts>("add_two_ints");
  beginner_tutorials::AddTwoInts srv;
  srv.request.a = atoll(argv[1]);
  srv.request.b = atoll(argv[2]);
  
  /**
  * 调用服务，向服务端发送请求，调用服务的过程是阻塞的，没有得到确切的结果就会阻塞
  */
  if (client.call(srv))
  {
    ROS_INFO("Sum: %ld", (long int)srv.response.sum);
  }
  else
  {
    ROS_ERROR("Failed to call service add_two_ints");
    return 1;
  }

  return 0;
}
```

