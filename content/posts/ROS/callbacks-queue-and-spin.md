---
title: "ROS回调处理"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["ROS"]
---

# ROS回调处理
## 概述

你订阅节点，或者发布服务进行响应的时候都会用到回调函数，它们会在正确的时刻被调用，比如消息到来时调用处理订阅消息的回调函数，请求到来时调用处理请求给出响应的回调函数。

## 回调函数队列

你需要明白的是，当某个事件发生的时候，不会立马调用回调函数，而是会把回调函数和对应的参数推入回调函数队列（callback queue）。你在调用subscribe方法的时候规定了固定大小的队列长度，因为你的机器的响应能力是有限的，有的时候没有能力处理所有的事件，只处理最相对较新的事件，老事件有可能移出队列。

## spin()和spinOnce()

然后会有一个或者多个线程来依次调用队列里面的回调函数（如果队列里有的话）。这个线程对ROS用户是透明的，你没办法操控它。但是你可以规定它到底处不处理回调函数，你如果没有明确给它说要调用队列里面的回调函数的话，它是不会调用回调函数的。控制是否调用回调函数的函数是`spin()`和`spinOnce()`

spin()的调用者会陷入阻塞，直到一些中断信号的发出。它会开启一个循环，处理所有队列里的回调函数，只有有就处理。

spinOnce()只让线程处理一个回调函数，就是队首的那个。这个函数可以让你按照一定频率调用回调函数处理消息。

## 全局队列与局部队列

一般情况下，对于你使用nodehandle创建的回调函数，容纳它的队列是ROS的全局队列，也就是说这个队列不仅容纳当前节点的回调函数，还有其它节点产生的回调函数，然后进行统一的调用。

当然，你可以让你的节点有一个单独的回调函数队列，不用全局的那个。但是你如果要调用这种队列里的回调函数，你需要使用callAvailable()或者callOne(), 相当于全局队列的spin()和spinOnce()。实际上你可以使用`ros::getGlobalCallbackQueue()`获取全局回调函数队列，spin()实际上是`ros::getGlobalCallbackQueue()->callAvailable()`，spinOnce实际上是`ros::getGlobalCallbackQueue()->callOne()`

```c++
#include <ros/callback_queue.h>

ros::CallbackQueue my_queue;
ros::NodeHandle nh;
nh.setCallbackQueue(&my_callback_queue);
```

甚至你可以让你的节点的部分回调函数进入一个专用的队列，给它们一个专用的包间。然后节点中的其它函数使用全局的回调函数队列，让它们吃大锅饭。你要实现这个功能的话可以使用带有Option对象的相应订阅或者处理请求的函数，在Option里规定使用哪个队列。具体怎么使用还是查文档吧。

## 什么时候使用节点特有的回调函数队列

1. 长时间运行服务。你给长期服务了一个队列，这样它就不会阻塞全局中其它回调函数了
2. 有计算时间长的回调函数，和长时间允许服务类似。

## 参考文档

[Callbacks and Spinning](https://wiki.ros.org/roscpp/Overview/Callbacks%20and%20Spinning)