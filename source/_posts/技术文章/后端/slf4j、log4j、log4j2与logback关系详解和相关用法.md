---
title: slf4j、Log4j、Log4j2与Logback关系详解和相关用法
sidebar: auto
titleTag: 原创
tags:
  - Java
  - 日志
sticky: 1
author:
  name: 木偶
  link: 'https://github.com/puppetdevz'
categories:
  - 技术文章
  - 后端
date: 2022-03-31 00:00:00
---



作为一名 Java 开发程序员，我们对项目中的日志打印肯定不会陌生，但是有没有发现，Java 的日志框架有很多，琳罗满目，这些框架有什么区别呢？有什么关系呢？在了解之后，仅以本文做一点粗浅的记录。

<!-- more -->

## I slf4j

slf4j 全称为：The Simple Logging Facade for Java

slf4j 是各种日志框架的简单门面或抽象（可以理解为类似 JPA 规范），而它的具体实现另有他人，例如 java.util.logging、Logback、Log4j 或 Log4j2。

我们的项目使用 SLF4J 仅需要添加一个强制依赖项，即 slf4j-api-1.x.xx.jar。

使用了 slf4j 之后，我们可以任意切换基于 slf4j 实现的日志框架（Log4j、Log4j2 或 Logback）

下面介绍的三种日志框架（Log4j、Log4j2 和 Logback）在编码中使用的方式都是一致的：

~~~java
public class DemoSlf4j {
    public static void main(String[] args) {
        Logger logger = LoggerFactory.getLogger(DemoSlf4j.class);
        logger.info("hello world");
    }
}
~~~

如果项目只是引入了 slf4j，而没有引入具体的实现，则会报错，如下：

运行后，报了如下错误：

![img](https://oss.puppetdev.top/image/note/0fd1e80416e526a14caf7f765cad0680.png)

## II Log4j

Log4j 是 apache 实现的一个开源日志组件（Wrapped implementations，即对 slf4j 的包装实现）

由于 Log4j 不是对 slf4j 接口的直接实现，因此如果使用 Log4j + slf4j，是需要加适配层。 

此外，Log4j 目前已不再更新维护。

### II.1 依赖引入

~~~xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.36</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.36</version>
</dependency>
~~~

添加了上面的依赖后，会引入以下三个包

<img src="https://oss.puppetdev.top/image/note/3fca1c7dca1732392f8c00ffa657c435.png" alt="image-20220401102656101" style="zoom:50%;" />

* log4j：适配层
* slf4j-api：slf4j规范
* `slf4j-log4j12`：接口具体实现层

其实只要引入 slf4j-log4j12，就会引入 slf4j-api，但是我们依旧推荐自己引入 slf4j-api，因为这样子我们可以自己控制日志门面版本，同时使我们具备在 Log4j、Log4j2 和 Logback 这类基于 slf4j 的日志框架间灵活的能力。

### II.2 配置文件

> 配置文件详解：[Log4j配置详解之Log4j.properties](https://www.iteye.com/blog/zengxiantao-1881700)

Log4j 的正常运行需要配置文件（**如果没有，则采用 Log4j 默认配置**），配置文件类型： Log4j.xml 或 Log4j.properties。

需要为其配置 root、appender、layout 等信息。下面以 xml 的方式进行配置

注意事项：

* 必须配置 root logger 和至少一个 appender。
* 日志输出级别，由高到低为：FATAL、ERROR、WARN、INFO、DEBUG

具体配置文件

~~~xml
<?xml version="1.0"  encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration>

    <!--若干个appender的定义-->
    <!--org.apache.log4j.ConsoleAppender 输出到控制台-->
    <appender name="myConsole" class="org.apache.log4j.ConsoleAppender">
        <!--输出格式-->
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern"
                   value="%-d{yyyy-MM-dd HH:mm:ss,SSS} [%c]-[%p] %m%n"/>
        </layout>
    </appender>

    <!--org.apache.log4j.DailyRollingFileAppender 每天产生一个日志文件-->
    <appender name="myFile" class="org.apache.log4j.DailyRollingFileAppender">
        <param name="File" value="output.log"/><!--文件位置-->
        <param name="Append" value="true"/><!--是否选择追加-->
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern"
                   value="%-d{yyyy-MM-dd HH:mm:ss,SSS} [%c]-[%p] %m%n"/>
        </layout>
    </appender>

    <!--org.apache.log4j.RollingFileAppender 滚动日志文件输出 文件大小到达一定尺寸时重新产生新的文件-->
    <!--<appender name="myFile" class="org.apache.log4j.RollingFileAppender">
        <param name="File" value="D:/output.log" />
        <param name="Append" value="true" />
        <param name="MaxFileSize" value="500KB"/>
        <param name="MaxBackupIndex" value="10" />
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%p (%c:%L)- %m%n" />
        </layout>
    </appender>-->

    <!--将各个包中的类日志输出到不同的日志文件中
        这样可以便于日志的分类。
        可以通过这个设置，把业务逻辑的日志添加到数据库。起到过滤的作用
    -->
    <!--这段配置的就是说把包名为“com.zjut.a1”且优先级为debug的日志通过myFile这个appender来处理。
    -->
    <category name="com.zjut.a1">
        <priority value="debug"/>
        <appender-ref ref="myFile"/>
    </category>


    <!-- 根logger的设置-->
    <root>
        <!--优先级设置，假设设置为“info”，则无法输出debug级别的日志-->
        <priority value="debug"/>
        <!--<priority value="info"/>-->
        <!--<priority value="warn"/>-->
        <!--<priority value="error"/>-->
        <!--<priority value="fatal"/>-->

        <!--添加刚才设置的appender-->
        <appender-ref ref="myConsole"/>
        <appender-ref ref="myFile"/>
    </root>
</log4j:configuration>
~~~

仅用于控制台输出日志的配置文件（复制可以直接用）

~~~xml
<?xml version="1.0"  encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration>
    <appender name="myConsole" class="org.apache.log4j.ConsoleAppender">
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern"
                   value="%-d{yyyy-MM-dd HH:mm:ss,SSS} [%c]-[%p] %m%n"/>
        </layout>
    </appender>
    <root>
        <priority value="debug"/>
        <appender-ref ref="myConsole"/>
    </root>
</log4j:configuration>
~~~

## III Logback

Logback 同样是由 Log4j 的作者设计完成的，拥有更好的特性，用来取代 Log4j 的一个日志框架，是 slf4j 的原生实现。（Native implementations，即直接实现了 slf4j 的接口）

Logback 当前分成三个模块：logback-core、logback-classic 和 logback-access。

logback-core 是其它两个模块的基础模块。logback-classic 是 log4j 的一个改良版本。

### III.1 依赖引入

~~~xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.36</version>
</dependency>

<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.11</version>
</dependency>
~~~

**添加以上依赖即可**。网上很多还添加了以下依赖：

~~~xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-core</artifactId>
    <version>1.2.11</version>
</dependency>
~~~

其实 logback-classic 这个项目本身就包含了 logback-core 和 slf4j-api，当我们引入 logback-classic，就会自动引入 logback-core 和 slf4j-api 了。

因此 logback-core 是可以不用引入的，但是我们依旧推荐自己引入 slf4j-api，因为这样子我们可以自己控制日志门面版本，同时使我们具备在 Log4j、Log4j2 和 Logback 这类基于 slf4j 的日志框架间灵活的能力。

例如从 logback 切换为 log4j2 我们只需要将 logback-classic 包替换为 

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.17.2</version>
</dependency>
```

### III.2 配置文件

> 详细参见：[官方文档](http://Logback.qos.ch/manual/index.html)

Logback 支持编程方式和配置文件的方式进行配置，同时官方网站中还提供了将 `log4j.properties` 转换为 `logback.xml` 的 web 工具，方便 Log4j 用户切换为 Logback。

此外，如果只是学习，自定义配置并不是必须的。如果程序没有检测到任何自定义配置，则会将日志直接打印到控制台中。

下面是 logback.xml 配置文件的配置内容（**如果没有，则采用 Log4j 默认配置**）：

~~~~xml 
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <!-- encoders are assigned the type
             ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
        <encoder>
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n</pattern>
        </encoder>
    </appender>

    <!-- Insert the current time formatted as "yyyyMMdd'T'HHmmss" under
         the key "bySecond" into the logger context. This value will be
         available to all subsequent configuration elements. -->
    <timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss"/>

    <appender name="FILE" class="ch.qos.logback.core.FileAppender">
        <file>testFile-${bySecond}.log</file>
        <append>true</append>
        <!-- encoders are assigned the type
             ch.qos.logback.classic.encoder.PatternLayoutEncoder by default -->
        <encoder>
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="DAYFILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logFile.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- daily rollover -->
            <fileNamePattern>logFile.%d{yyyy-MM-dd}.log</fileNamePattern>

            <!-- keep 30 days' worth of history capped at 3GB total size -->
            <maxHistory>30</maxHistory>
            <totalSizeCap>3GB</totalSizeCap>

        </rollingPolicy>

        <encoder>
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="debug">
        <appender-ref ref="STDOUT"/>
        <appender-ref ref="DAYFILE"/>
        <appender-ref ref="FILE"/>
    </root>
</configuration>
~~~~

## IV Log4j2

Log4j2 相对于 Log4j 和 Logback 是比较晚出现的。它由 Apache 推出，是对 Log4j 的升级，但与 Log4j 有很大的区别。

Log4j2 在 Log4j 上做了很多重大的改进，还吸取了 Logback 很多优良的设计，同时还修复了 Logback 架构上的一些问题。

可以说，Log4j2 是 slf4j 日志框架体系下的集大成者，是目前最优秀的 Java 日志框架，没有之一。

根据官方的测试表明，在多线程环境下，Log4j2 的异步日志表现更加优秀。在异步日志中，Log4j2 使用独立的线程去执行 I/O 操作，可以极大地提升应用程序的性能。 

### IV.1 依赖引入

~~~xml
<!--slf门面，可以省略，但是不建议-->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>1.7.36</version>
</dependency>
<!--适配层-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.17.2</version>
</dependency>
<!--Log4j2具体的日志实现-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.17.2</version>
</dependency>
~~~

因此 slf4j-api 是可以不用引入的，但是我们依旧推荐自己引入 slf4j-api，因为这样子我们可以自己控制日志门面版本，同时使我们具备在 Log4j、Log4j2 和 Logback 这类基于 slf4j 的日志框架间灵活的能力。

### IV.2 配置文件

（**如果没有，则采用 Log4j 默认配置**）

## V Log4j/Logback/Log4j2性能比较

### V.1 异步性能比较

<img src="https://oss.puppetdev.top/image/note/46c4671dd720db36186b6c1e70d0ebe7.png" alt="image-20220401110328469" style="zoom:50%;" />

其中，`Loggers all async` 是基于 [LMAX Disruptor](http://lmax-exchange.github.com/disruptor/) 实现的，可见 Log4j2 的异步日志性能是最棒的。

## VI Log4j/Logback/Log4j2选用总结

* 对于项目而言

由于 Log4j 已经停止更新维护，所以一般只会在 Logback 和 Log4j2 中进行选择。对日志的性能要求比较高的，可以选择 Log4j2。（所以现在一般都是采用 Log4j2 了，不再使用 Logback，因为相同的场景下，Log4j2 的 TPS 比 Logback 高好几倍）

试验参考：[日志框架选择Logback还是Log4j2？](https://juejin.cn/post/6844903926777511943)

* 对于个人学习而言

个人学习一般只是用于简单的日志记录，三个框架随意选择。

## VII 全篇总结与补充

* slf4j 是 Java 的一个日志门面，实现了日志框架一些通用的 api，Log4j、Log4j2 和 Logback 是具体的日志框架。
* Log4j2 可以说是目前 Java 日志框架中最为优秀的一个。
* Log4j、Log4j2 和 Logback 均可以通过配置来自定义日志输出规则（格式、方式和策略等），如果没有，则都会采用其默认的配置。

* 日志框架可以单独的使用，也可以结合 slf4j 一起使用。
  * 单独使用，分别调用框架自己的方法来输出日志信息。
  * 结合 slf4j 一起使用，调用 slf4j 的 api 来输入日志信息，具体使用与底层日志框架无关（需要底层框架的配置文件）

**但是强烈推荐结合 slf4j 使用日志框架**。这样子我们可以在 Log4j、Log4j2 和 Logback 这类基于 slf4j 的日志框架间随意切换，更加灵活。
