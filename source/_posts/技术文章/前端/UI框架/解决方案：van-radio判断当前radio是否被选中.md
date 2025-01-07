---
title: 解决方案：van-radio判断当前radio是否被选中
sidebar: auto
tags:
  - Vant
  - 解决方案
publish: false
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
  - 前端
  - UI框架
date: 2022-10-21 00:00:00
sticky:
---



<!-- more -->

## 场景

当我点击 van-radio 时，我需要先判断当前点击的 van-radio 是否已经处于**被选中的状态**

## 解决方案

1. 给 van-radio 设置 click 事件，并绑定事件处理函数
2. 将当前 van-radio 的 name 值传递进去
3. 在事件处理函数中判断：`selectedRadioName === nameValue`，如果相等，说明当前 van-radio 已经处于被选中状态了，否则，未选中。

