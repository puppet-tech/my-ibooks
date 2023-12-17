---
title: jdk中java、javax、sun和org包的区别
sidebar: auto
tags:
  - Java
  - JDK
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
date: 2022-08-18 00:00:00
sticky:
---



JDK 中有以下一类基础类库包，java、javax、sun、org，这些包有什么区别呢？

<!-- more -->

ava、javax、org、sun 包都是 jdk 提供的类包，且都是在 rt.jar 中。rt.jar 是 JAVA 基础类库（java 核心 [框架](https://so.csdn.net/so/search?q = 框架 & spm=1001.2101.3001.7020)中很重要的包），包含 lang 在内的大部分功能，而且 rt.jar 默认就在根 classloader 的加载路径里面，所以放在 classpath 是多此一举 。他们之间的区别具体如下：

## `java.*`

java SE 的标准库，是 java 标准的一部分，是对外承诺的 java 开放接口，通常要保持向后兼容，一般不会轻易修改。包括其他厂家的在内，所有 jdk 的实现，在 java.* 上都是一样的。

## `javax.*`

也是 java 标准的一部分，但是没有包含在标准库中，一般属于标准库的扩展。通常属于某个特定领域，不是一般性的 api。

所以以扩展的方式提供 api，以避免 jdk 的标准库过大。当然某些早期的 javax，后来被并入到标准库中，所以也应该属于新版本 JDK 的标准库。比如 jmx，java 5 以前是以扩展方式提供，但是 jdk5 以后就做为标准库的一部分了，所有 javax.management 也是 jdk5 的标准库的一部分。

## `com.sun.*`

是 sun 的 hotspot 虚拟机中 `java.*` 和 `javax.*` 的实现类。因为包含在 rt 中，所以我们也可以调用。但是因为不是 sun 对外公开承诺的接口，所以根据实现的需要随时增减，因此在不同版本的 hotspot 中可能是不同的，而且在其他的 jdk 实现中是没有的，调用这些类，可能不会向后兼容，**所以一般不推荐使用**。

## `org.omg.*`

是由企业或者组织提供的 java 类库，大部分不是 sun 公司提供的，同 `com.sun.*`，不具备向后兼容性，会根据需要随时增减。其中比较常用的是 w3c 提供的对 XML、网页、服务器的类和接口。
