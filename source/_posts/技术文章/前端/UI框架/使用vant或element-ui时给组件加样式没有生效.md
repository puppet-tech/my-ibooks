---
tags:
  - 解决方案
  - Vue
  - Vant
  - ElementUI
permalink: /pages/9e5248/
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
  - 前端
  - UI框架
title: 使用vant或element-ui时给组件加样式没有生效
date: 2022-10-05 00:00:00
sticky:
---



<!-- more -->

## 1.1 场景

下面举例：

当通过组件调用 van-dialog 时，dialog 内容过多，对话框没有出现滚动样式，现通过以下的 css 语句解决该问题

```css
.van-dialog__content {
    max-height: 400px;
    overflow: scroll;
}
```

结果发现，样式没有生效。

## 1.2 解决方案

采用以下方式，添加 `/deep/`，让其**作用域**往下钻 

```css
/deep/ .van-dialog__content {
    max-height: 400px;
    overflow: scroll;
}
```

样式生效，问题解决

## 1.3 拓展说明

在 Vue 中，关于样式有几个规范：

- 一是样式写在文件的最后。
- 二是使用 class，而不直接为标签名称写样式。
- 三是要 scoped。

尤其是 scoped 很重要，因为 Vue 并不是我们传统的一个页面一个页面的文件，如果不 scoped，会发生样式干扰。

假如我们使用了一个 van-grid-item 组件，我们通过检查，发现生成 HTML 的结构是：

- 外层 div 套了一个内层 div。
- 外层 div 的 class 是：van-grid-item。
- 内层 div 的 class 是：van-grid-item__content。

于是我们写样式：

```css
<style lang="less" scoped>
	.van-grid-item { 
        border:1px solid red; 
	}
	.van-grid-item .van-grid-item__content { 
        background: #f00; 
        color: #fff; 
	}
</style>
```

我们会发现第一个生效了，但是第二个没生效，这是因为 Vue 只认组件本身那层 class，其内部继续产生的 class 是不认的，怎么解决呢？加上 /deep/ 让其作用域往下钻，如下：

```css
<style lang="less" scoped>
	/deep/.van-grid-item .van-grid-item__content { 
        background: #f00; 
        color: #fff; 
	}
</style>
```



