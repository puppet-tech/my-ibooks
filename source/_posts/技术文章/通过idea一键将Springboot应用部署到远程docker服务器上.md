---
title: 通过idea一键部署Springboot应用为容器到远程docker服务器上
sidebar: auto
tags:
  - IDEA
  - 运维
  - SpringBoot
  - Docker
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
date: 2022-08-02 00:00:00
sticky:
---

<!-- more -->

[[toc]]

主要参考博客教程：<https://segmentfault.com/a/1190000042012007>

期间遇到一些错误：

- idea 无法通过 SSH 连接到 docker，报错误："java.io.IOException: unexpected end of stream on <http://127.0.0.1/>…"

  解决方案：<https://intellij-support.jetbrains.com/hc/en-us/community/posts/4416763906322-Trouble-connecting-to-docker-via-ssh>
