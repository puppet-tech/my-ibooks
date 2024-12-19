---
title: 解决方案：npm install报错
tags:
  - npm
  - 解决方案
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
sidebar: auto
categories:
  - 技术文章
  - 前端
date: 2022-03-17 00:00:00
---



## 1.1 pm ERR! code EINTEGRITY

### 1.1.1 问题描述

```shell
：pm ERR! code EINTEGRITY
```

![image-20220317132008831](https://oss.puppetdevz.top/image/note/0df6ae6fb0bd9e492fb05a3d8d15b2d3.png)

### 1.1.2 问题解决

1. 删除本地的 package-lock.json 文件
2. 【可选】更新自己的 npm 版本 （npm i -g npm）
3. 【可选】清空 npm 缓存 （npm cache clean --force | npm cache verify）
4. 重新安装 （npm install）

## 1.2 Can't find Python executable python

### 1.2.1 问题描述

```
stack Error: Can't find Python executable python
```

### 1.2.2 问题解决

因为 node-gyp 的安装需要，它只能支持 python2，官方推荐 python2.7，

1. 下载安装 python 2.7，并设置系统路径

2. 终端中进行设置：`npm config set python "C:\Python27\python.exe"`
