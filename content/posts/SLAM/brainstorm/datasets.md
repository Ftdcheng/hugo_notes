---
title: "SLAM数据集"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["brainstorm"]
---

# SLAM数据集

这里是一些SLAM相关的数据集，提供描述和链接

CoVLA

* 简介

  包含视觉、语言和动作的大规模标注数据集。这是一种基于CoVLA数据集的新型VLA模型，用于可解释的端到端自动驾驶。

* 相关链接

  [turing-motors/CoVLA-Dataset-Mini · Datasets at Hugging Face](https://huggingface.co/datasets/turing-motors/CoVLA-Dataset-Mini)

Semantic KITTI

* 简介

  SemanticKITTI 对 KITTI **360° 激光雷达扫描点云**（Velodyne 数据）进行了逐点标注。

  **类别数量**：分为 **28个语义类别**（其中有19个主要类别用于评估，其他为未标注或忽略类别）。

  **语义类别示例**：

  - **静态物体**：道路、建筑物、树木、草地、路标等。

  - **动态物体**：行人、车辆、骑行者、动物等。

  - **其他**：天空、未标注区域等。

  - 在 SemanticKITTI 中，动态物体（如行人、骑行者、车辆等）与静态物体（如道路、建筑物、树木等）被明确区分，方便研究动态环境下的语义分割和物体检测任务。

    这对于开发能适应 **动态环境的 SLAM** 或感知系统非常有帮助。