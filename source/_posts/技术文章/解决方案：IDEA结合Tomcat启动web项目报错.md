---
title: 解决方案：IDEA结合Tomcat启动Web项目报错
sidebar: auto
tags:
  - Java
  - Web
  - IDEA
  - Tomcat
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
date: 2022-04-13 00:00:00
---





<!-- more -->

[[toc]]

## Servlet [DispatcherServlet] in web application [] threw load() exception

### 问题描述

![image-20220428114132435](http://img.puppetdev.top/image/note/f04a0dc90d8e5d17b1e3e2e49633b463.png)

### 原因分析&&解决方案

根据报错信息，可以发现是无法找到 DispatcherServlet 类，

进一步查看编译输出目录下时候有 lib 目录

<img src="http://img.puppetdev.top/image/note/5f7506f4006c04a35b99396119cb4275.png" alt="image-20220428114412254" style="zoom:50%;" />

如果没有，可能是依赖库没有加载加载进来，或者放置的位置有问题，进一步查看

<img src="http://img.puppetdev.top/image/note/cf82a2b57215281a12bcf4b6d9c9875a.png" alt="image-20220428114645183" style="zoom:50%;" />

