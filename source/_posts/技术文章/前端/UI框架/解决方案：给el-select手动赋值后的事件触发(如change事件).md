---
title: 解决方案：给el-select手动赋值后的事件触发（如change事件）
sidebar: auto
tags:
  - ElementUI
  - 解决方案
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
  - 前端
  - UI框架
date: 2022-04-13 00:00:00
---

<!-- more -->

## 环境

vue、elementUI、vscode

## 问题描述

在实现业务编辑时，采用 dialog 内嵌表单，当点击“编辑”按钮，数据传入 dialog 并回显到表单中，其中表单有一个select 下拉框，回显时，输入回显到下拉框中，select 选中的值由`''` 变成具体的值，此时我的本意是监听这个变化，尝试调用el-select 的 change 事件，但结果发现，手动赋值并不会触发 el-select 的 change 事件

## 解决方案

经查阅，尚未发现更好的解决方法，只能在进入的时候，手动触发事件。

