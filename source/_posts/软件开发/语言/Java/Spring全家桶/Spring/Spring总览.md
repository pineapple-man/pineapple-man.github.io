---
title: Spring 总览
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: Spring
keywords: Spring
excerpt: Spring 是一个轻量级的框架，但是总感觉学习的知识比较零零碎碎。本文主要是一篇概括性文章，用于总结 Spring 的所有相关知识
date: 2022-01-08 17:48:26
thumbnailImage:
---
<!-- toc -->

## Spring 概述

:thinking:如今这么火热的 Spring 是什么 ？
{% alert success no-icon %}

Spring 是 J2EE **轻量级**的开源 `JavaEE` 框架，通过 Spring 框架能够简化了企业应用开发的复杂性

{% endalert %}

{% alert info no-icon %}

Spring 具有如下特性：

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/202112301657650.png %}

:notes: Spring 有两个核心部分:`IOC`和`AOP`
{% alert success no-icon %}

- IOC：控制反转，将创建对象过程交给Spring进行增加
- AoP：面向切面编程，能够在不修改源代码的情况下进行功能增加「开闭原则」

{% endalert %}

Spring 5 框架的指导原则：
{% alert success no-icon %}

- Provide choice at every level
- Accommodate diverse perspectives
- Maintain strong backward compatibility
- Care about API design
- Set high standards for code quality

{% endalert %}

## IoC

IoC( Inversion of Control ,控制反转)，是面向对象编程中的一种设计原则，可以用来降低代码之间的耦合度，IoC 中最常见的方式有两种：依赖注入（DI）和依赖查找（DL），如果不理解什么是 IoC 可以{% post_link "Java/Spring全家桶/Spring/IOC-DI" "点击这里"%}详细了解设计模式中的 IoC 的原理

:thinking: IoC 到底做了什么？
{% alert success no-icon %}

1. 控制反转：将对象创建以及两个对象间调用的过程，交给 Spring 进行管理
2. 管理资源：Spring 容器主动的将资源推送给需要的组件，开发人员不需要知道容器的创建过程，只需要接受资源

{% endalert %}

在 Spring 中通过三种方式能够实现 IoC：XML 解析、工厂模式和反射，可以通过{% post_link Java/Spring全家桶/Spring/SpringIoC "点击这里" %}详细了解 Spring 中的 IoC 使用方式

## Aop

:question:什么是 AoP
{% alert info no-icon %}

AoP 称之为面向切面（方面）编程，目的是：在不通过修改源代码的方法，在主干功能里面添加新功能，遵从设计原则中的「开闭原则」，类似于`python`中的装饰器。AoP 的作用就是将业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的**耦合度**降低

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/202112301657855.png %}

{% post_link Java/Spring全家桶/Spring/AOP "详细了解 Spring 中的AoP"%}


## 事务管理

在 JavaEE 企业级开发的应用领域，为了**保证数据的完整性和一致性**，必须引入数据库事务的概念，所以事务管理是企业级应用程序开发中必不可少的技术，Spring 框架中的事务管理也是一个重点。在 Spring 框架中存在两种事务管理方式：**编程式事务管理**和**声明式事务管理**。推荐在项目开发中使用声明式事务，进行项目中的事务管理

## Webflux

Webflux 是 Spring 5 增加的新模块，用于 Web 开发，功能和 SpringMVC 类似，Webflux 使用当前一种比较流行的响应式编程出现的框架

## JdbcTemplate

Spring 框架对 JDBC 进行封装，使用 JdbcTemplate 方便实现对数据库操作，又是一套 JDBC 操作的模板，（可以跳过不深入学习）后续对数据库的操作有更高的 ORM 框架

## Spring 注解驱动开发
{% alert info no-icon %}

配置文件开发方式，方便新手入门学习并了解`Spring`中的相关定义，但是编写方式十分繁琐，不容易维护，错误难以排查，以及对项目配置文件的维护增加了项目的复杂度，所以在 SpringBoot 和 SpringCloud 兴起之后，{% hl_text red %} Spring 的注解驱动开发就用的非常多了{% endhl_text %}!。学习 Spring 中常用的注解能够提高开发效率

{% endalert %}


## 附录

[视频链接]:https://www.bilibili.com/video/BV1gW411W7wy
[官方中文文档]:https://lfvepclr.gitbooks.io/spring-framework-5-doc-cn/content/
[官方英文文档]:https://docs.spring.io/spring-framework/docs/current/reference/html/index.html
[官方文档]:https://docs.spring.io/spring-framework/docs/current/reference/html/index.html
