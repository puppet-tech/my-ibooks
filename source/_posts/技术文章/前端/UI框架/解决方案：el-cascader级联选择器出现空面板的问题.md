---
title: 解决方案：el-cascader级联选择器出现空面板的问题
tags:
  - ElementUI
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
# Cascader级联选择器出现空面板的问题

> vue、element、el-element

<img src="https://oss.puppetdev.top/image/note/0a8653f711ba357386a3201f78c03b6e.png" alt="image-20210617122750695" style="zoom: 67%;" />

出现空白面板是因为树形结构的数据的**最底层 children 是个空数组**。遇到这种问题，前端可以通过递归，将 children 长度为 0 的节点对象的 children 属性删除，或将值赋为 undefined

```js
cleanData(itemList) {  // 将根节点的 children 传入，传入的只是数据对象的引用
    for (const item of itemList) {
        if (item.children.length === 0) delete item.children
        else this.cleanData(item.children)
    }
    return itemList 
}
```

