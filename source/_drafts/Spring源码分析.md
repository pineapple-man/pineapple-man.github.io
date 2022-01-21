---
title: Spring源码分析（一）
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: Java
tags: Spring
keywords: Spring
excerpt: Spring 源码分析第一节，主要记录如何搭建 Spring 源码分析环境
---

## 概述

Spring 整个工厂的启动称之为 `refresh` 刷新工厂

`DefaultListableBeanFactory` 是 Spring 的档案馆，报错所有的 Bean 信息

Spring 底层源码中使用最多的就是模版模式

Spring 的底层通过后置增强机制，完成很多功能



这个功能 Spring 在启动的时候注入了什么组件？

这个功能牵扯到的组件在什么位置被什么后置增强器增强成了什么样子？



## 附录

[spring源码导入](https://blog.csdn.net/KKALL1314/article/details/113450506)
[使用IDEA+Gradle构建Spring5源码并调试（手把手教程全图解）](https://www.cnblogs.com/mazhichu/p/13163979.html)
[SpringBoot2.3.X源码编译之Gradle](https://blog.csdn.net/zxs9999/article/details/113511447)