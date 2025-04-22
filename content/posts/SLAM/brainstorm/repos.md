---
title: "SLAM代码库"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["brainstorm"]
---

# SLAM代码库

这里是一些开源的SLAM相关的代码库，提供内容描述和资源链接

## RoadLib

* 作者：

​	武汉大学GREAT实验室，leader是李星星。他们是做卫星和导航的

* 简介：

​	**一个开源的基于道路标识的增量式建图与定位系统**

​	算法部分主要包括**道路标识提取与建模**、**局部增量式建图**、**地图管理**与**地图匹配定位**等模块。

​	好像是单目的。

* 相关链接：

​	[RoadLib 代码库](https://github.com/GREAT-WHU/RoadLib)

​	[预览视频1](https://www.bilibili.com/video/BV1bp42117N1)

​	[预览视频2](https://www.bilibili.com/video/BV1dS421d71n)

​	[武汉大学开源RoadLib：基于道路标识的增量式SLAM！](https://mp.weixin.qq.com/s/_tT5t0kh3_oiwWlbdEPLlg)

## MonoLaneMapping

* 简介

​	实时单目摄像机车道建图。IROS23（IEEE的一个会议，智能机器人系统国际会议） 已经接受了这篇论文

这个框架接受图片和里程计（比如VIO）信息，估计位姿和车道地图。

* 作者：

​	香港科技大学航空机器人实验室，leader是沈邵劼，香港科技大学-大疆联合创新实验室主任。

* 相关链接

​	[HKUST-Aerial-Robotics/MonoLaneMapping: Online Monocular Lane Mapping Using Catmull-Rom Spline (IROS 2023)](https://github.com/HKUST-Aerial-Robotics/MonoLaneMapping)

## A-LOAM

* 简介

  LOAM的一个高级的实现，没有复杂的数学推导，没有冗余的计算，代码更好看，适合新手。

  代码库里使用的传感器有`VLP-16`，`Velodyne HDL-64`

* 作者

  香港科技大学航空机器人实验室

* 相关链接

  [HKUST-Aerial-Robotics/A-LOAM: Advanced implementation of LOAM](https://github.com/HKUST-Aerial-Robotics/A-LOAM)

* 图示

  ![kitti_gif](D:\ftdcheng\Desktop\文档\笔记\imgs\slam-repo\a-loam_kitti_gif.gif)

