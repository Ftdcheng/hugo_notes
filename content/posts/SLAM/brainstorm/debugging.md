---
title: "代码调试问题"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["brainstorm"]
---

# 代码调试问题

## livox ros driver找不到

关掉所有的vscode终端，开一个新终端，在你的项目目录自己新建build，然后cmake。
因为vscode终端的环境和cmake插件使用的环境是一样的，所以你source之后，那么cmake config就可以找到livox ros driver。