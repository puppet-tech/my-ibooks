---
title: 解决方案：ElementUI的input组件的autofocus失效（自动获取焦点）
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
date: 2022-04-13 00:00:00
sticky:
---


<!-- more -->


## 问题描述

这两天在开发中碰到需要在 el-table 表单中嵌套 input 组件，可以在表单上直接进行编辑，我使用了 template 的具名插槽进行实现，如下：

```vue
<el-table-column prop="toApplyNum" label="申请数量">
  <template slot-scope="scope">
    <el-input v-model="scope.row.toApplyNum"></el-input>
  </template>
</el-table-column>
```

但实际运行后，发现 input 控件无法正常输入，每输入一个字符，就会自动失焦，需要再次点击之后才能继续输入。

## 问题分析

经在网上查阅资料发现

autofocus 是 vue 中 input 的原生属性，element也支持这种方法。但是 element 中的 el-input 组件外面还有其他组件，导致 autofocus 失效

## 解决方案

知道了原因，尝试采用手动调用 focus 方法来解决问题。

1. 先给 el-input 控件绑定一个 ref，由于要使得每一行均能操作，需要绑定为一个动态 ref

```vue
<el-input :ref="'inputRef'+ scope.row.id" v-model="scope.row.toApplyNum" />
```

2. 监听el-input 控件的 input 事件

```vue
<el-input 
  :ref="'inputRef'+ scope.row.id" 
  v-model="scope.row.toApplyNum"
  @input="handleInput" />
```

3. 每当输入，就手动会 input 控件获取焦点

```js
this.$nextTick((goodsInfo) => {
  let refName = 'inputRef' + goodsInfo.id;
  this.$refs[refName].focus();
})
```