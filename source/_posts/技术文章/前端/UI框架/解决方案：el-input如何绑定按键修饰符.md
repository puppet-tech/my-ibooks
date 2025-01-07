---
title: 解决方案：el-input如何绑定按键修饰符
tags:
  - 解决方案
  - ElementUI
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
sidebar: auto
categories:
  - 技术文章
  - 前端
  - UI框架
date: 2022-09-21 22:14:44
---


vue 允许将按键值作为修饰符来使用，如监听回车事件，有两种写法，如下代码：

```HTML
<input type="text" @keyup.13="console.log($event)"></input>
<input type="text" @keyup.enter="console.log($event)"></input>
```


当我们在 el-input 采用如上两种写法时，他是不生效的。el-input 监听不到键盘事件,原因是 element-ui 是封装组件，所以 el 标签属于自定义标签，因此触发不了键盘事件。

**解决办法** ：加上 `.native` 原生事件修饰符即可。

`.native`：监听组件根元素的原生事件，主要作用是给自定义的组件添加原生事件。vue 与 elementUI 中给 el-input 绑定键盘按键代码如下：

```HTML
<el-input 
  placeholder="店铺名称" 
  clearable 
  v-model="queryObj.shopname" 
  @keyup.enter.native="query">
  <template slot="prepend">店铺名称</template>
</el-input>
```



