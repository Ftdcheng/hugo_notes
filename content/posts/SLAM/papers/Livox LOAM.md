---
title: "Livox LOAM"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["papers"]
---

# Livox LOAM

使用Livox固态雷达实现的LOAM。
## 数据处理

1. 使用了强度来区分不同的材质，比如可以检测到一面墙中墙到门窗的变化
2. 对接收到的点云进行了分片处理，将不同的片放到不同的CPU上处理，使得处理明显变快。将一个帧分成三个子帧。每个子帧分别进行运动补偿。