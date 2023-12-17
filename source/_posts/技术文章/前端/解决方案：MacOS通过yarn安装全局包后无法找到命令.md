---
title: 解决方案：通过yarn安装全局包后无法找到命令
sidebar: auto
tags:
  - 包管理器
  - yarn
  - 解决方案
publish: true
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
  - 前端
date: 2022-09-21 00:00:00
sticky:
---



<!-- more -->

## 背景描述

* 操作系统：macOS
* 通过 npm 全局安装的 yarn

## 问题描述

通过命令 `yarn global add packageName` 后，试图该模块的命令，发现无法找到

<img src="http://img.puppetdev.top/image/note/4fe618e399628724ad20b4a6323b0a59.png" alt="image-20210930135631984" style="zoom:50%;" />

## 问题分析

macOS 在安装完 npm 后，再通过 npm 安装 yarn。则 yarn 相关配置和包路径默认位于家目录下

<img src="http://img.puppetdev.top/image/note/5dada55a4915bb85a40e68e0f75354cd.png" alt="image-20210930135818676" style="zoom:50%;" />

通过 yarn global add 安装包，则会被安装到 `~/.config/yarn/global/node_modules/` 中

![image-20210930140102368](http://img.puppetdev.top/image/note/791f480a09702d4bbc1f5d8213509e76.png)

而该路径是没有配置在环境变量中的，因此无法找到。

## 解决方案

将上述路径配置到环境变量中即可。此处我是配置在 `~/.zshrc` 中，大家可以视自身所用的 shell 自行决定。

配置如下：

![image-20210930140654119](http://img.puppetdev.top/image/note/8e5e85ebf430d7676443903be1407465.png)

~~~shell
export PATH="$PATH:`yarn global bin`:$HOME/.config/yarn/global/node_modules/.bin"
~~~

配置后保存，执行 `source ./.zshrc`

结果如下：

<img src="http://img.puppetdev.top/image/note/ad02e37701153120d62feef66115b282.png" alt="image-20210930140835904" style="zoom:50%;" />

