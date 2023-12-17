---
title: Postman四种Body类型之间的区别
sidebar: auto
tags:
  - Postman
  - 工具
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
date: 2022-04-13 00:00:00
sticky:
---

使用 Postman 模拟请求，并想要在请求 Body 中携带数据一起发送时，我们会发现 Postman 的 Body 类型有很多，这些类型具体有什么区别呢？

<!-- more -->

postman 的 body 类型有四种

1. form-data
2. x-www-form-urlencoded
3. raw
4. binary

注意：如果使用 Postman 模拟请求头带有中文参数的请求，需要先将中文进行 URL 编码。

## form-data

等价于 http 请求中的 `multipart/form-data`，它会将表单的数据处理为一条消息，以标签为单元，用分隔符分开，既可以上传键值对，也可以上传文件。

当上传的字段是文件时，会有 Content-Type 说明文件类型

content-disposition，用来说明字段的一些信息

由于有 boundary 隔离，所以 `multipart/form-data` 既可以上传文件，也可以上传键值对，它采用了键值对的方式，所以可以上传多个文件。

## x-www-form-urlencoded

等价于 `application/x-www-from-urlencoded`，会将表单内的数据转换为键值对，比如 `name=java&age=23`

## raw

可以上传任意格式的文本，可以上传 text、json、xml、html 等

## binary

相当于 `Content-Type:application/octet-stream`，从字面意思上看，只可以上传二进制数据，通常用来上传文件，由于没有键值，所以一次只能上传一个文件。

## multipart/form-data 与 x-www-form-urlencoded

- `multipart/form-data`：可以上传文件等二进制数据，也可以上传表单键值对，最后会转化为一条信息
- `x-www-form-urlencoded`：只能上传键值对，并且键值对都是间隔分开的
