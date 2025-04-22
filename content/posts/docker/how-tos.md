---
title: "how to do something in Docker"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["docker"]
---

# how to do something in Docker

## 如何让容器能够借用宿主机显示GUI
1. 启动容器时挂载/tmp/.X11-unix
2. 设置环境变量DISPLAY
3. 宿主机上允许docker访问X11
```bash
xhost +local:docker
```
没有X11服务器得自己装。

## 如何让你的容器默认使用清华源
```bash
RUN sed -i 's|http://archive.ubuntu.com/ubuntu/|https://mirrors.tuna.tsinghua.edu.cn/ubuntu/|g' /etc/apt/sources.list && \
    sed -i 's|http://security.ubuntu.com/ubuntu|https://mirrors.tuna.tsinghua.edu.cn/ubuntu|g' /etc/apt/sources.list
```

## 怎么在构建镜像的时候开代理
通过--buil-arg传递环境变量，在linux下，你的主机的ip是172.17.0.1，如果你的主机在7897端口开启了代理服务，那么你的命令可以这样写
```bash
docker build --build-arg http_proxy=http://172.17.0.1:7897 --build-arg https_proxy=http://172.17.0.1:7897 --build-arg -t name:image .
```
如果你使用docker compose，你的compose.yaml可以这样写：
```yaml
services:
    app:
        build:
            context: .
            args:
                - http_proxy=http://172.17.0.1:7897
                - https_proxy=http://172.17.0.1:7897
```

## 怎么在构建docker时就执行source devel/setup.bash

你需要ENTRYPOINT，这个命令可以提前帮你source

```bash
cd /root/hku_ws
catkin_make
source devel/setup.bash

exec "$@"
```

然后你的Dockerfile这样写：

```dockerfile
COPY ./entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD [ "bash" ]
```

## 如何避免烦人的键盘布局选择

```bash
DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends && \
ros-noetic-pcl-ros ros-noetic-eigen-conversions
```