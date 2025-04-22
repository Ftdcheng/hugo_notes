---
title: "网络"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["docker"]
---

# 网络

docker网络可以让容器访问外网或者互相访问。

默认开启了docker网络，不过容器不知道自己连接的什么对象，只知道自己的IP，网关，DNS这些网络参数。

当你启动docker时，会默认将容器挂在default这个bridge驱动上。

默认桥接网络只能通过IP地址进行互相访问。比如你在默认桥接网络下有两个容器，那么你只要知道了两个容器的IP地址你才能进行两个容器之间的通信。在用户定义桥接网络下，你可以使用容器名或者别名进行互相访问。

## 用户自定义网络

你可以创建用户自定义网络把多个容器放到同一个网络下，互相进行通信。默认使用`bridge`，也就是桥接的方式创建网络。这是一个创建并连接到自定义网络的例子：

```bash
docker network create -d bridge my-net
docker run --network=my-net -itd --name=container3 busybox
```

## 驱动
除了`bridge`外，容器可以使用很多网络驱动。
1. bridge: 同一主机上的容器进行通信使用，默认选项
2. 

## 连接到网络
容器可以连接到多个网络