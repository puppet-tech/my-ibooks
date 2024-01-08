---
tags:
  - Jenkins
  - 解决方案
title: Jenkins作业日志太大导致Jenkins崩溃
---

![image.png](https://oss.puppetdev.top/image/note/d7d95225cef35da6a867289e072bb864.png)

Jenkins 流水线运行时由于某些原因，Console Output 日志太大了，导致 Jenkins 主节点内存溢出，程序崩溃。

<!--more-->

## 1.1 环境说明

- Jenkins 2.426
- Docker方式部署

## 1.2 解决方案

我们的最终核心目的是避免 Jenkins 崩溃，因此想到了对 Console Output 日志的大小进行限制


