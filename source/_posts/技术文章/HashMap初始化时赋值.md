---
title: Java集合初始化时赋初值
tags:
  - Java
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
sidebar: auto
categories:
  - 技术文章
date: 2022-02-18 00:00:00
---



在 Java 中，HashMap、ArrayList 和 HashSet 等是常用的数据结构，如果我们想在初始化时就直接给它们赋值，该怎么写呢？（而不是创建一个空的集合后，用 put/add 等方法添加进去）

<!-- more -->

## HashMap

```java
HashMap<String, String> map = new HashMap<String, String>() {
    {
        put("name", "test");  
        put("age", "20"); 
    }
};
```

说明：这里的双括号 `{{}}` 是什么意思？

第一层实际是定义了一个匿名内部类(Anonymous Inner Class)。

第二层实际上是一个实例初始化块(instance initializer block)，这个块在内部匿名类构造时被执行。

## ArrayList

```java
List<String> names = new ArrayList<String>() {
    {
        for (int i = 0; i < 10; i++) {
            add("A" + i);
        }
    }
};
```

## 初始化时直接赋值的缺点

**如果这个对象要串行化，可能会导致串行化失败。**原因如下：

* 上述实现是匿名内部类的声明方式，所以引用中存有外部类的引用。因此，当要串行化这个集合时，外部类也会被串行化，而此时如果外部类没有实现 serialize 接口时，就会报错。

  可以在外部类中实现 serialize 接口。

* 上述实现其实是声明了一个继承自 HashMap 的子类。但是有些串行化方式，例如通过 Gson 串行化为 json，或要串行化为 xml 时，类库中提供的方式无法串行化 HashSet 或者 HashMap 的子类，就会导致串行化失败。

  解决方案：只能乖乖重新初始化集合对象 —— `new HashMap(map);`

-----------

## 2022-07-05更

后看到 Oracle 官网发布的 [JEP 269: Convenience Factory Methods for Collections (openjdk.org)](https://openjdk.org/jeps/269)

里面就有谈到，不建议使用上述的方式去初始化集合的初值，上面的方式可能会导致**内存溢出**或者**序列化相关的问题**，文档原话如下：

> Another alternative is to use the so-called "double brace" technique:
>
> ```java
> Set<String> set = Collections.unmodifiableSet(new HashSet<String>() {{
>     add("a"); add("b"); add("c");
> }});
> ```
>
> This uses the instance-initializer construct in an anonymous inner class, which is a bit prettier. However, it is quite obscure, and it costs an extra class at each usage. It also holds hidden references to the enclosing instance and to any captured objects. This may cause memory leaks or problems with serialization. For these reasons, this technique is best avoided.

那么我们要怎么做呢？如果是 JDK 9 及其之后的版本，JDK 新增一些相关的方法，例如

```java
Set<String> set = Set.of("a", "b", "c");

List.of(a, b, c);

Map.of();
Map.of(k1, v1);
Map.of(k1, v1, k2, v2);
Map.of(k1, v1, k2, v2, k3, v3);
Map.ofEntries(Map.Entry<K,V>...);
```

但上述的方式，JDK 8 是不支持的，JDK 8 的采用如下方式

```java
Set<String> set = Collections.unmodifiableSet(Stream.of("a", "b", "c").collect(toSet()));

Arrays.asList("a", "b", "c");
```

但可用的方法很有限，并且依旧十分 verbose，如果无法忍受，则可以用第三方的库，例如 Google 的 Guava 库，里面就提供了很多好用的 api
