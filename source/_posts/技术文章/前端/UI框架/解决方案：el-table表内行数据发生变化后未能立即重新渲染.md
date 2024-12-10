---
title: 解决方案：el-table表内部某行数据发生变化时，未立即重新渲染
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
sticky:
---



<!-- more -->

## 问题描述

这两天在实现表单内嵌 el-input 的时候（同：[问题：el-table表内行数据发生变化后未能立即重新渲染](https://github.com/puppetdevz/notes-related/blob/main/notes-related/前端/项目实战经验-vue+el|vant/问题集合/问题：elementUI的input组件autofocus失效.md)）遇到一个问题：当输入在 input 中输入数量之后，表单没有反应，不会监听表单数据的变化而重新渲染。

## 问题分析

本身 el-table 就不支持监听数据列表中某个数据的字段变化而自动再次渲染

## 解决方案

在 el-table 中添加一个 key，可以设置成 boolean 类型的，表单数据列表中的某个数据字段更新后更新这个 key

```vue
<el-table 
  :data="toApplyGoodsInfoList"
  :key="updateKey">
  ...
 </el-table>
```

该key 需要在 data 中定义

```js
export default {
  data() {
    return {
      updateKey: false  // 辅助变量，值不重要
    }
  }
}
```

当表单数据发生变化时，更新这个 updateKey

```js
handleInput(val, goodsInfo) {
  goodsInfo.toApplyNum = val
  this.updateKey = !this.updateKey  // 值不重要，有更新就好，直接取反
  // 重新获取 input 的焦点
  this.$nextTick(() => {
    let refName = 'inputRef' + goodsInfo.id
    this.$refs[refName].focus()
  })
}
```