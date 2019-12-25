---
layout: post
title: Springboot 命令注入属性[--]&[-D]
category: Spring Boot 
tags: [Spring Boot]
---

>Springboot 命令注入属性[--]&[-D]

## 场景

在用`Jenkins`，做自动化部署时，遇到一些命令问题。

需要通过命令的形式，注入些业务值。


## -D 系统属性注入

`Java`，启动jar 命令：

`java [ options ] -jar file.jar [ arguments ]`

`Java HotSpot VMs`针对，当前运行时环境，提供了一套调准的`options`

`-Dproperty=value`

```
-Dproperty=value
Sets a system property value.

If value is a string that contains spaces, then you must enclose the string in double quotation marks:

java -Dmydir="some string" SomeClass
```

## -- 属性赋值

![](https://oscimg.oschina.net/oscnet/f7df6bb9124ffc8bf199a4e7d05232d6b4e.jpg)


## 验证


把参数打印出来

![](https://oscimg.oschina.net/oscnet/870b3f640d75d5d1791ad5d33a5e954b5d3.jpg)

`java -jar` 启动应用

![](https://oscimg.oschina.net/oscnet/fbab3d71de55c252eb6566ada08f7ecceed.jpg)

![](https://oscimg.oschina.net/oscnet/e638f489a8dac081844c85b07a69027940e.jpg)

端口号变成了：`2000`

注意，`-D`命令，需要在`jar`包之前。

![](https://oscimg.oschina.net/oscnet/0fcb853658826cf331a81c82334235a50a9.jpg)

`application.properties`中的属性，也可以通过`-D`的形式，注入。

![](https://oscimg.oschina.net/oscnet/174c7ce5fed8ac85cae76c28dcec35ed540.jpg)

![](https://oscimg.oschina.net/oscnet/2e38bbdf1f0ebeff46de29c1be049308006.jpg)