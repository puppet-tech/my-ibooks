---
title: JDK8至JDK17各版本重要特性更新
sidebar: auto
tags:
  - Java
  - JDK
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
date: 2022-04-13 00:00:00
sticky:
---





<!-- more -->

[[toc]]

## JDK8新特性（2014年初）（LTS版本）

1. Lambda 表达式
2. 函数式编程
3. 接口可以添加默认方法和静态方法，也就是定义不需要实现类实现的方法
4. 方法引用
5. 重复注解，同一个注解可以使用多次
6. 引入 Optional 来避免空指针
7. 引入 Streams 相关的 API
8. 引入新的 Date/Time 相关的 API
9. 新增 jdeps 命令行，来分析类、目录、jar 包的类依赖层级关系
10. JVM 使用 MetaSpace 代替了永久代（PermGen Space）

**重要特性：Lambda 表达式、函数式接口、方法引用、Stream 流式 API、采用 MetaSpace 代替了永久代（PermGen Space）**

## JDK9新特性（2017年9月）

1. 接口方法可以使用 private 来修饰
2. 设置 G1 为 JVM 默认垃圾收集器
3. 支持 http2.0 和 websocket 的 API

**重要特性：主要是 API 的优化，如支持 HTTP2 的 Client API、JVM 采用 G1 为默认垃圾收集器**

## JDK10新特性（2018年3月）

1. 局部变量类型推断，类似 JS 可以通过 var 来修饰局部变量，编译之后会推断出值的真实类型
2. 并行 Full GC，来优化 G1 的延迟
3. 允许在不执行全局 VM 安全点的情况下执行线程回调，可以停止单个线程，而不需要停止所有线程或不停止线程

**重要特性：通过 var 关键字实现局部变量类型推断，使 Java 语言变成弱类型语言、JVM 的 G1 垃圾回收由单线程改成多线程并行处理，降低 G1 的停顿时间**

## JDK11新特性（2018年9月）（LTS版本）

1. ZGC，ZGC 可以看做是 G1 之上更细粒度的内存管理策略。由于内存的不断分配回收会产生大量的内存碎片空间，因此需要整理策略防止内存空间碎片化，在整理期间需要将对于内存引用的线程逻辑暂停，这个过程被称为 "Stop the world"。只有当整理完成后，线程逻辑才可以继续运行。（并行回收）
2. Flight Recorder（飞行记录器），基于 OS、JVM 和 JDK 的事件产生的数据收集框架
3. 对 Stream、Optional、集合 API 进行增强

**重要特性：对于 JDK9 和 JDK10 的完善，主要是对于 Stream、集合等 API 的增强、新增 ZGC 垃圾收集器**

## JDK12新特性（2019年3月）

1. Shenandoah GC，新增的 GC 算法、
2. switch 表达式语法扩展，可以有返回值
3. G1 收集器的优化，将 GC 的垃圾分为强制部分和可选部分，强制部分会被回收，可选部分可能不会被回收，提高 GC 的效率

**重要特性：switch 表达式语法扩展、G1 收集器优化、新增 Shenandoah GC 垃圾回收算法**

## JDK13新特性（2019年9月）

1. Socket 的底层实现优化，引入了 NIO；
2. switch 表达式增加 yield 关键字用于返回结果，作用类似于 return，如果没有返回结果则使用 break；
3. ZGC 优化，将标记长时间空闲的堆内存空间返还给操作系统，保证堆大小不会小于配置的最小堆内存大小，如果堆最大和最小内存大小设置一样，则不会释放内存还给操作系统；
4. 引入了文本块，可以使用 """ 三个双引号表示文本块，文本块内部就不需要使用换行的转义字符；

**重要特性：ZGC 优化，释放内存还给操作系统、socket 底层实现引入 NIO**

## JDK14新特性（2020年3月）

1. instanceof 类型匹配语法简化，可以直接给对象赋值，如 if(obj instanceof String str), 如果 obj 是字符串类型则直接赋值给了 str 变量；
2. 引入 record 类，类似于枚举类型，可以向 Lombok 一样自动生成构造器、equals、getter 等方法；
3. NullPointerException 打印优化，打印具体哪个方法抛的空指针异常，避免同一行代码多个函数调用时无法判断具体是哪个函数抛异常的困扰，方便异常排查；

## JDK15新特性（2020年9月）

1. 隐藏类 hidden class；
2. 密封类 sealed class，通过 sealed 关键字修饰抽象类限定只允许指定的子类才可以实现或继承抽象类，避免抽象类被滥用；

## JDK16新特性（2021年3月）

1. ZGC 性能优化
2. instanceof 模式匹配
3. record 的引入

JDK16 相当于是将 JDK14、JDK15 的一些特性进行了正式引入

## JDK17新特性（2021年9月）（LTS版本）

1. 正式引入密封类 sealed class，限制抽象类的实现；
2. 统一日志异步刷新，先将日志写入缓存，然后再异步刷新；

虽然 JDK17 也是一个 LTS 版本，但是并没有像 JDK8 和 JDK11 一样引入比较突出的特性，主要是对前几个版本的整合和完善

## 参考

* [JDK8到JDK17各个版本的重要特性整理 - Lucky帅小武 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jackion5/p/10772017.html)