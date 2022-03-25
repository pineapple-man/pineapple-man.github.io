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
excerpt: 今天突然看到一个名词：JMX，那么什么是 JMX ？它是否和 JVM 有关系？本文将对 JMX 进行展开学习
---

## 概述
:question:什么是JMX？

{% blockquote 维基百科 https://zh.wikipedia.org/wiki/JMX "Java Management Extensions" %}
JMX ( Java Management Extensions )即 Java 管理扩展，是 Java 生态中一种工具。这个工具可以管理和监控 Java 应用程序、系统对象、设备（如打印机）的资源占用情况，并且最终这些资源信息存储在 `Managed Bean` 抽象对象中

{% endblockquote%}
简而言之，JMX 就是一个 Java 平台的管理和监控接口，用于对运行中的程序进行监控，通过这一套接口最终可以方便的查看一个 Java 程序运行时占用的内存，分配了多少内存，有多少活动线程，有多少休眠线程等信息

## JMX 架构
为了标准化管理和监控，Java平台使用JMX作为管理和监控的标准接口，任何程序，只要按JMX规范访问这个接口，就可以获取所有管理与监控信息。实际上，常用的运维监控如Zabbix、Nagios等工具对JVM本身的监控都是通过JMX获取的信息，因为JMX是一个标准接口，不但可以用于管理JVM，还可以管理应用程序自身。下图是JMX的架构：

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/jmx.png %}

## 资源管理
JMX把所有被管理的资源都称为MBean（Managed Bean），这些MBean全部由MBeanServer管理，如果要访问MBean，可以通过MBeanServer对外提供的访问接口，例如通过RMI或HTTP访问。

## 案例分析
## 附录
[集成JMX](https://www.liaoxuefeng.com/wiki/1252599548343744/1282385687609378)

[wiki](https://en.wikipedia.org/wiki/Java_Management_Extensions)

[JMX超详细解读](https://www.cnblogs.com/dongguacai/p/5900507.html)