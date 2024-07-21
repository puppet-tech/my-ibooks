---
title: 解决方案：van-dialog内容过多时却不会变成滚动样式的问题
tags:
  - Vant
  - 解决方案
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
sidebar: auto
categories:
  - 技术文章
  - 前端
date: 2022-09-21 22:14:44
---
# 解决van-dialog内容过多却没有滚动样式的问题

## 场景

通过组件调用 Dialog 时，van-dialog 内嵌套其他组件，其内容过多，Dialog 不会自动出现滚动样式，导致无法显示底部按钮

![image-20210708143224495](https://oss.puppetdev.top/image/note/ef727b324fb70c17dba47541cc6a0ec8.png)

## 解决方案

在样式中做如下设置

```css
/deep/ .van-dialog__content {
    max-height: 400px;
    overflow: scroll;
}
```

**说明：**先设置最大行高，然后再给溢出部分设置 scroll 滚动样式。