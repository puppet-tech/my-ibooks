---
title: 解决方案：使用@Async或@Transactional注解失效
sidebar: auto
tags:
  - Spring
  - SpringBoot
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
  - 后端
date: 2022-07-19 00:00:00
---



本文记录个人在使用 @Async 和 @Transactional 注解时，遇到的问题及对应的解决方案


<!-- more -->

参考如下：

* [分析使用@Async或@Transactional注解失效（解决使用AopContext.currentProxy()出现"Cannot find current proxy“）_Dongguabai的博客-CSDN博客](https://blog.csdn.net/Dongguabai/article/details/80788585)
* [从@Async案例找到Spring框架的bug：exposeProxy=true不生效原因大剖析+最佳解决方案【享学Spring】 - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1497700)
* [解决No thread-bound request found: Are you referring to request attributes outside of an actual web re_lvxiucai的博客-CSDN博客](https://blog.csdn.net/lvxiucai/article/details/101758179)