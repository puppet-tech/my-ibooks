---
tags: 
title: SpringBoot单体应用故障检测、现场保留与恢复
---

使用 SpringBoot 搭建单体应用，并用类似以下命令在 Linux 服务器上启动应用运行。

```
nohup java -jar -Xms1024m -Xmx1536m -XX:NewRatio=2 -XX:SurvivorRatio=8 -XX:MetaspaceSize=256m -XX:MaxMetaspaceSize=256m $JAR_NAME -Dspring.config.location=./application.yml &
```

运行过程中，因服务器资源不足，或应用接口设计不规范导致应用崩溃，我们该如何及时、甚至提前检测到故障发生，并将应用运行现场环境（堆栈日志、异常日志信息等）

<!--more-->

Actuator

Netdata

编写一个 Go 程序，当程序异常，自动调取重启

OMM 策略，自动杀掉重启

资源弹性伸缩
