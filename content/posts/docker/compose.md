---
title: "Docker Compose"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["docker"]
---

# Docker Compose

## Compose 文件
默认的Compose文件是`compose.yaml`

## Service
### build属性
build属性可以在启动服务时构建镜像作为service。如果有image属性的话，build出来的镜像会推送到registry。

> 注意：image和build同时存在的话默认使用image，也就是说如果你的主机上有image规定的镜像那么就用它，如果没有就拉取image，你的build不会触发。要想触发你的build，你得修改`pull_policy`属性，可以设置为build。
