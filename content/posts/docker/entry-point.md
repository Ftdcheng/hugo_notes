---
title: "Dockerfile Entrypoint"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["docker"]
---

# Dockerfile Entrypoint
[Docker Entry Point 官方文档](https://docs.docker.com/reference/dockerfile/#entrypoint)

ENTRYPOINT最主要的功能是将容器变成一个“命令”, 容器在启动的时候会执行ENTRYPOINT规定的命令。

ENTRYPOINT和CMD可以配合使用，CMD给容器提供默认参数。

不过如果用户在docker run的时候在最后给出了参数，那么给出的参数会覆盖默认参数。

CMD在单独使用的时候是容器启动时执行的默认命令，和ENTRYPOINT配合使用的时候是默认选项。