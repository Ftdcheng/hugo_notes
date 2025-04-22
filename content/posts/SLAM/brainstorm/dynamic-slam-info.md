---
title: "动态SLAM的一些信息"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["brainstorm"]
---

# 动态SLAM的一些信息
1. 静态假设

   SLAM的正常工作依赖于动态假设，如果忽视环境中的动态物体，会给配准、地图带来影响。

2. 动态SLAM的方法

   基于去除的方法：一种是手工设计的特征算法对动态点进行去除，另外一种是通过机器学习的方式找到动态点。有使用预先建立的地图和当前scan进行比对找到动态点的。还有一些人是直接等建图完了，直接对鬼影下手清除的。基于地图的方法离线的居多。

   基于追踪的方法：对动态物体进行追踪，对动态点进行去除。有先生成BBOX的，然后再追踪BBOX的变化

3. benchmark

   [KTH-RPL/DynamicMap_Benchmark: The First Dynamic Map Removal Benchmark | Included 8 SOTA methods | Continous updating](https://github.com/KTH-RPL/DynamicMap_Benchmark)

4. 关注作者

   [Qingwen Zhang](https://kin-zhang.github.io/)

   

