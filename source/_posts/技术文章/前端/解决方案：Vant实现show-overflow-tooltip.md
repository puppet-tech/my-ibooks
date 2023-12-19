---
title: 解决方案：移动端实现触碰时show-overflow-tooltip
sidebar: auto
tags:
  - Vant
  - 解决方案
publish: true
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
  - 前端
date: 2022-04-13 00:00:00
sticky:
---


<!-- more -->


## 应用场景

用于编写 PC 端的 UI 框架 Element-UI 的 Table 组件有一个属性叫 show-overflow-tooltip 属性，可以实现当单元格的文字太多时，用省略号(ellipsis)展示，鼠标覆上时显示文字全文本的提示框效果。如下：

![image-20211129165847396](https://oss.puppetdev.top/image/note/a3ad5f8cec0fbac62c27424dbae96a64.png)

这阵子在项目碰到一个问题，由于我主要是做后端的，对前端并不是很熟悉，但被迫做了一阵子前端。项目组用的技术栈是 Vue + Vant，移动端的 UI 框架，需要实现一个方框里当文字太多时，用省略号代替超出的文本，当手触碰时，又能够显示所有文本。

找了半天，没发现 Vant 有提供与 ElementUI 类似的，只能自己摸索写了一个

## 解决方案

~~~css
.myClass {
    text-overflow: ellipsis;
    overflow : hidden;
    white-space: nowrap;
}

.myClass:hover {
    text-overflow: clip;
    white-space: normal;
    word-break: break-all;
}
~~~

后边发现，Stack Overflow 上有类似的提问，并且有了相关解答，其中一个解决跟我的解决差不多，贴一下地址：https://stackoverflow.com/questions/5474871/html-how-can-i-show-tooltip-only-when-ellipsis-is-activated

