---
title: "配准方法"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["brainstorm"]
---

# 配准方法
## ICP
论文：[A method for registration of 3-D shapes](https://ieeexplore.ieee.org/document/121791)
## generialized-ICP （泛化ICP）
论文：[Generalized-icp](https://www.roboticsproceedings.org/rss05/p21.pdf)

如果两个扫描之间没有扫描到同一个物体，ICP将会很难配准，为了解决这个问题，除了点到点的距离，泛化ICP结合了点到面距离的ICP
## LOAM 改进的generialzed-ICP
论文：[Loam: Lidar odometry and mapping in real-time](https://www.ri.cmu.edu/pub_files/2014/7/Ji_LidarMapping_RSS2014_v8.pdf)
在泛化ICP的基础上添加了点到边的距离
## 基于3D关键点的方法
* Learning informative point classes for the acquisition of object model maps
* Fast point feature histograms (fpfh) for 3d registration
* Fast 3d recognition and pose using the viewpoint feature histogram

这类方法从稠密的点云中提取特征，减少计算资源的占用。
诸多方法已经使用并行化来提高效率