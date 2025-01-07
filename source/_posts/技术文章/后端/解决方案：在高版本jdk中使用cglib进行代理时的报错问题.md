---
title: 解决方案：在高版本jdk中使用cglib进行代理时的报错问题
sidebar: auto
tags:
  - 解决方案
  - JDK
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
  - 后端
date: 2022-11-28 14:10:20
---
## Unable to make protected final java.lang.Class java.lang.ClassLoader.defineClass

### 问题描述

在使用 Spring 集成的 cglib 手动对某个类进行代理增强时报了以下错误：

Unable to make [protected](https://so.csdn.net/so/search?q=protected&spm=1001.2101.3001.7020) final java.lang.Class java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain) throws java.lang.ClassFormatError accessible: module java.base does not "opens java.lang" to unnamed module @5c29bfd

我的代码为：

```java
public class CglibProxyForMultiInterfacesTest {
    interface Service1 {
        void m1();
    }

    interface Service2 {
        void m2();
    }

    public static void main(String[] args) {
        Enhancer enhancer = new Enhancer();
        enhancer.setInterfaces(new Class[]{Service1.class, Service2.class});
        enhancer.setCallback(new MethodInterceptor() {
            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                System.out.println("方法：" + method.getName());
                return null;
            }
        });

        Object proxy = enhancer.create();
        if (proxy instanceof Service1) {
            ((Service1) proxy).m1();
        }
        if (proxy instanceof Service2) {
            ((Service2) proxy).m2();
        }
        System.out.println(proxy.getClass());
        for (Class<?> anInterface : proxy.getClass().getInterfaces()) {
            System.out.println(anInterface);
        }
    }
}
```

### 解决方案

貌似是高版本的 JDK 禁用了反射权限，需要添加 JVM 参数去开启

```sh
--add-opens java.base/java.lang=ALL-UNNAMED  
```

![image-20221128141538748](https://oss.puppetdevz.top/image/note/26068c2c1d8b57fd7b41650b68e1086f.png)
