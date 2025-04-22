---
title: "激光雷达SLAM"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["brainstorm"]
---

# 激光雷达SLAM
## LOAM
论文：[Loam: Lidar odometry and mapping in real-time](https://www.ri.cmu.edu/pub_files/2014/7/Ji_LidarMapping_RSS2014_v8.pdf)
为了减少运动模糊，LOAM采用了雷达姿态线性插值，也有用后端优化的方法减少运动模糊的方法（In2laama: Inertial lidar localisation autocalibration and mapping）。后端优化的方法相对准确一点，但是不能实时运行。
## LeGO-LOAM
论文：[Lego-loam: Lightweight and ground-optimized lidar odometry and mapping on variable terrain](https://ieeexplore.ieee.org/document/8594299)
## LOAM livox
论文：[Loam livox: A fast, robust, high-precision lidar odometry and mapping package for lidars of small fov](https://ieeexplore.ieee.org/document/9197440)
提出了新的减少运动模糊的方法，并且并行化了。提出的方法比LOAM的线性插值更精确，运行效率更高。

