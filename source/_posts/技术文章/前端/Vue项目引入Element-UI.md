---
title: Vue项目引入Element-UI
tags:
  - ElementUI
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
sidebar: auto
categories:
  - 技术文章
  - 前端
date: 2022-09-21 22:14:44
---
一套为开发者、设计师和产品经理准备的，基于 vue 的桌面端组件库。目前有基于 vue 2.0 的，也有 基于 vue 3.0 的。下面主要介绍基于 vue 2.0 的

其官网地址为：https://element.eleme.cn/#/zh-CN

> 基于 vue 3.0 的官网为：https://element-plus.gitee.io/#/zh-CN

## 通过命令行安装配置

1. 安装依赖包

   ```shell
   npm install element-ui -S
   ```

2. 导入 Element-UI 资源

   ```js
   // 导入组件库
   import ElementUI from 'element-ui';
   // 导入组件相关样式
   import 'element-ui/lib/theme-chalk/index.css';
   // 将 ElementUI 注册到 vue 中
   Vue.use(ElementUI);
   ```

   ![image-20210425115123893](http://img.puppetdev.top/image/note/3beabd4a0d41ca69c14a7a846cf50eff.png)

3. 使用 element-ui，在 App.vue 中使用；从 elemet-ui 的官网中拷贝对应的元素的代码过来。

   ![image-20210425115454749](http://img.puppetdev.top/image/note/40dccbfcca8e6be55b49ece6802a5fd8.png)

## 通过图形化界面安装

1. 运行 vue ui 命令，打开图形化界面

2. 通过 vue 项目管理器，进入具体项目配置面板

3. 点击 “插件” -> “添加插件”，进入插件查询面板

4. 搜索 vue-cli-plugin-element 并安装

5. 配置插件，按需导入，减少打包后项目的体积

   ![image-20210425120032949](http://img.puppetdev.top/image/note/e7eca2c3516e495a4eb12d09339e7915.png)

   **注意：一定要修改为“按需导入”，否则 vue-cli 会全部导入，项目打包后，体积会很大。**