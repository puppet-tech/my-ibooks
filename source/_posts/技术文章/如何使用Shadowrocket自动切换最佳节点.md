---
title: 如何使用Shadowrocket自动切换最佳节点
tags:
  - 科学上网
publish: false
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
sidebar: auto
categories:
  - 技术文章
date: 2022-02-13 00:00:00
sticky:
---



Shadowrocket 有一个功能是可以自动对列表节点进行定时测速并切换到最快节点，如果某个节点无法访问，会自动进行切换而不需要手动进行干预，下面就是设置的方法。

<!-- more -->

## 开启简单模式

新版本的 Shadowrocket（2.2.2）支持” 简单模式 “直接开启” 自动切换最佳节点 “功能。

![image-20220304100414118](http://img.puppetdev.top/image/note/cafe3d2f37579721f4f13c13f97e4f7a.png)

经由上面的简单设置后，默认所有节点为一个分组，且会自动进行测速，并切换为最佳的节点。

另外建议在②图中，开启” 启用回退 “功能，这样子如果遇到刚切换到最佳节点而最佳节点不能用了的极端情况，也能够保证节点可用。

若不想默认所有节点为一个分组，也可以自己设置分组，在④图中，从” 分组 “进入，挑选节点做相应的设置即可。

## 高级设置（自动切换的时间间隔等等）

Shadowrocket—> 全局路由 —> 分组 —>URL 测试设置

下图为每 10 分钟测试一次，5s 无应答视为节点超时，当一个节点的响应时间 + 10ms 仍旧比当前使用节点快时，切换至更快节点。

<img src="http://img.puppetdev.top/image/note/f5600a06f65b3e351c630deb60e19f05.png" alt="image-20220304093859314" style="zoom:50%;" />

