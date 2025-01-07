---
title: 解决方案：ElementUI的dialog中无法获取到内部表单的引用
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

项目中使用到了 dialog 组件去实现业务的新增和编辑操作，这两个操作共用一个表单，因此每次显示 dialog 时，需要调用 `$refs.form.resetFields()` 方法去重置表单，但发现无法获取到 form 引用，调用会报错。

```vue
<el-dialog title="新增/编辑" :visible.sync="dialogVisible" width="100%">
  <el-form ref="form">
    ...
  </el-form>
</el-dialog>

this.$refs.form.resetFields()  // => 获取不到 form，为 undefined
```

## 问题分析

**elementUI 的官方说法** ：Dialog 的内容是懒渲染的，即在第一次被打开之前，传入的默认 slot 不会被渲染到 DOM 上。因此，如果需要执行 DOM 操作，或通过 `ref` 获取相应组件，请在 `open` 事件回调中进行。

所以要解决问题，要么就在 dialog 的 open、close、closed 等回调函数中进行操作。要么，就得使用 `this.$nextTick(() => {...})` ，等待 DOM 渲染完毕后再去调用。

## 解决方案

### 采用`this.$nextTick`

```JavaScript
this.$nextTick(() => { 
  this.$refs.form.resetFields()
})
```

## 利用 dialog 的回调函数

```JavaScript
export default {
    methods: {
        close() {
          this.$refs['form'].resetFields()
        }
    }
}
```

