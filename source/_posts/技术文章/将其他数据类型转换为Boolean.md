---
title: 将整数型转为Boolean类型
sidebar: auto
tags:
  - Java
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
date: 2022-02-18 13:04:00
---



跟 Python 不同，Java 中的非 0 不能直接为 true

<!-- more -->

## 方式1

```java
 boolean myBoolean = myInt != 0;
```

## 方式2-三目语句

```java
 int a = 1;  // 带转化 int 整数
 boolean result = (a == 0) ? false : true;  // 转化语句
```

