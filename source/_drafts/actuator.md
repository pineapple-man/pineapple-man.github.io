---
title: Actuator
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: SpringBoot
keywords: Actuator
excerpt: 本文主要介绍 Actuator
---

## 概述
:question:什么是`Spring Boot Actuator`?
{% alert success no-icon%}
Spring Boot Actuator 模块提供了许多生产级别的功能，比如：**健康检查**、**审计**、**指标收集**、**HTTP 跟踪**等，帮助我们监控和管理Spring Boot 应用。这个模块是一个采集应用内部信息暴露给外部的模块，上述的功能都可以通过HTTP 和 JMX 访问。
{%endalert%}

因为暴露内部信息的特性，Actuator 也可以和一些外部的应用监控系统（Prometheus，Influx等）整合，这些监控系统提供了出色的仪表盘，图形，分析和警报，可帮助管理员统一友好的界面，监视和管理应用程序
Actuator使用Micrometer与这些外部应用程序监视系统集成。这样一来，只需很少的配置即可轻松集成外部的监控系统。

Micrometer 为 Java 平台上的性能数据收集提供了一个通用的 API，应用程序只需要使用 Micrometer 的通用 API 来收集性能指标即可。Micrometer 会负责完成与不同监控系统的适配工作。这就使得切换监控系统变得很容易。
对比 Slf4j 之于 Java Logger 中的定位。


## 附录

[Spring Boot Actuator 模块 详解：健康检查，度量，指标收集和监控](https://segmentfault.com/a/1190000021611510)

[Spring Boot (十九)：使用 Spring Boot Actuator 监控应用](http://www.ityouknow.com/springboot/2018/02/06/spring-boot-actuator.html)