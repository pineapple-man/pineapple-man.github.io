---
title: JMX
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: 
- Java
tags: JVM
keywords: JMX
excerpt: 本文主要介绍JMX
---

## 概述
:question:什么是JMX？
{% alert success no-icon%}
JMX是Java Management Extensions，它是一个Java平台的管理和监控接口。
{%endalert%}
:question:为什么需要JMX？
{% alert success no-icon%}
在所有的应用程序中，对运行中的程序进行监控都是非常重要的，Java应用程序也不例外。我们肯定希望知道Java应用程序当前的状态，例如，占用了多少内存，分配了多少内存，当前有多少活动线程，有多少休眠线程等等。如何获取这些信息呢？
为了标准化管理和监控，Java平台使用JMX作为管理和监控的标准接口，任何程序，只要按JMX规范访问这个接口，就可以获取所有管理与监控信息
{%endalert%}
实际上，常用的运维监控如Zabbix、Nagios等工具对JVM本身的监控都是通过JMX获取的信息
因为JMX是一个标准接口，不但可以用于管理JVM，还可以管理应用程序自身。下图是JMX的架构：

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/jmx.png)
## 附录
[集成JMX](https://www.liaoxuefeng.com/wiki/1252599548343744/1282385687609378)

[wiki](https://en.wikipedia.org/wiki/Java_Management_Extensions)

[JMX超详细解读](https://www.cnblogs.com/dongguacai/p/5900507.html)