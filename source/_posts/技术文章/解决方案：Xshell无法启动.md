---
title: 解决方案：xshell无法启动
sidebar: auto
tags:
  - 解决方案
  - 软件
  - Xshell
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
date: 2022-04-13 00:00:00
---

xshell 无法启动，提示：“要继续使用此程序，您必须应用最新的更新或使用新版本”。

<!-- more -->

[[toc]]

在使用 xshell 6 时，启动要求更新版本，否则无法正常启动

<img src="https://oss.puppetdev.top/image/note/8da0dca3f0f4e83d5b97c9db2d8ac0da.png" alt="image-20210222110448584" style="zoom:67%;" />

## 解决方案

原理：修改时间

### 方案一

修改电脑系统时间，例如修改为 18 年 12 月 25 日之前的时间

但该方案看起来就很蠢，临时使用还好，不然为了使用这个软件还得去改时间。

### 方案二

修改 nslicense.dll 文件。该文件为一个二进制文件，需要使用特别的编辑器才能打开，否则会乱码，例如使用 C32asm、UltraEdit，我使用的是 UltraEdit，可以直接百度搜索，官网下载。该软件需要付费使用，可试用 30 天，用来改个文件绰绰有余。(也可以自己想办法破解)

下载安装好 UltraEdit 后，打开 xshell 的安装目录，找到目录下的 nslicense.dll，用 UltraEdit 打开，Ctrl + F 搜索 “7F 0C 81 F9 80 33 E1 01 0F 86 81”，将 86 改为 83，保存。**注意备份 nslicense.dll 文件，避免失败重装**

完成。
