---
title: 'Docker导入进行报错：invalid diffID for layer xx：expected “sha256:xxx'
sidebar: auto
tags:
  - Docker
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
date: 2022-12-01 15:30:45
---
## 1.1 问题描述

docker 导入镜像 tar 包报错，报错信息如下：

![image-20221201153103996](https://oss.puppetdevz.top/image/note/e0a9732c86ce643f614c7b76901e1100.png)

## 1.2 解决方案

通过报错信息可以看出，是导入的时候，docker 对 SHA256 哈希码进行校验，没有校验通过，说明这个镜像 tar 包被修改过。为了验证，我们可以利用 MD5 对镜像进行比对

1. 在通过 `docker save 镜像名称/id -o demo.tar` 命令导出镜像 tar 包后，计算出 tar 包的 MD5 值

   ```sh
   md5sum demo.tar  # md5sum 是 Linux 自带的命令
   ```

2. 将 demo.tar 从 Linux 下载下来后，再次对 demo.tar 计算 MD5 值

   ```sh
   certutil -hashfile demo.tar MD5  # certutil 是 Windows 自带的程序
   ```

计算后，对比上面两步的 MD5 值，如果不一致，说明从 Linux 将 demo.tar 包下载下来时，可能因为网络等原因，导致数据发生了改变，从而产生这个错误。只需要重新从 Linux 下载 demo.tar 即可，下载后再次计算 MD5 进行对比，直到一致。
