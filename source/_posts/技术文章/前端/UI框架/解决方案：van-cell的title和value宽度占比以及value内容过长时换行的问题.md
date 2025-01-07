---
title: 解决方案：van-cell的title和value宽度占比以及value内容过长时换行的问题
sidebar: auto
categories:
  - 技术文章
  - 前端
  - UI框架
tags:
  - Vant
publish: false
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
date: 2022-10-21 00:00:00
sticky:
---



<!-- more -->

1. van-cell 组件默认 title 和 value 宽度为各占 50%，如果想要增加 value 的宽度，可以给当前的组件的 `van-cell__value` 设置 min-width
2. `van-cell__value` 本身是右对齐，但是换行之后，最后一行需要左对齐而不是右对齐，解决方法如下面的 span 样式

```css
.address-cell {
    .van-cell__value {
      min-width: 70%;
      span {
        display: inline-block;
        text-align: left;
        word-break: break-all;
      }
    }
  }
```





