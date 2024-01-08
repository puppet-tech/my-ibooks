---
title: 解决方案：SpringBoot-前端时间传递到后端少了8个小时的问题
sidebar: auto
tags:
  - 解决方案
  - SpringBoot
author:
  name: 木偶
  link: https://github.com/puppetdevz
categories:
  - 技术文章
  - 后端
date: 2022-11-09 11:25:00
---
## 问题描述

前端向后端发送 POST 请求，请求数据为 JSON 格式，其中有个字段为日期，后端 SpringBoot 通过 Date 类型去接收入参，结果发现比当前时间少了 8 个小时。

初步分析是时区导致的。

## 解决方案

第一步：

```yml
spring:
	jackson:
		// 注意格式不要写错了，例如 HH 不要写出 hh，否则可能会有其他问题
	    date-format: yyyy-MM-dd HH:mm:ss
	    time-zone: GMT+8
```

第二步：时区用 `Asia/Shanghai` 或者 `GMT%2B8`

```yml
    url: jdbc:mysql://127.0.0.1:3306/aaaaa?
    useUnicode=true&
    characterEncoding=utf-8&
    useSSL=true&
    serverTimezone=Asia/Shanghai
```



