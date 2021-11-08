---
title: Eureka 学习笔记
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: Spring 全家桶
tags: 服务注册中心
keywords: 服务注册中心
excerpt: Eureka
---
# 概述

Spring Cloud封装了Netflix 公司开发的Eureka模块来实现服务治理

## 服务治理

在传统的 RPC 远程调用框架中，每个服务于服务之间依赖关系十分复杂，管理起来比较繁琐，所以需要使用一种智能的服务治理（管理）软件，来管理服务于服务之间依赖关系，实现服务调用、服务发现、服务注册、负载和容错等

## 服务注册与发现

Eureka采用了 CS 的设计架构，Eureka Server 作为服务注册功能的服务器，它是服务注册中心，而系统中的其他微服务，使用Eureka的客户端连接到 Eureka Server 并维持心跳连接。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行

在服务注册与发现中，存在一个**注册中心**。当服务器启动的时候，各个服务提供者会把当前自己服务器的信息（比如**服务地址通讯地址**等以别名方式）**注册到注册中心上**。服务消费者使用该别名的方式去注册中心上获取到实际的服务通讯地址，然后再进行RPC调用

:sparkles:RPC远程调用框架核心设计思想：**注册中心**，因为使用注册中心管理每个服务与服务之间的一个依赖关系(服务治理概念)

:notes:在任何RPC远程框架中，都会有一个注册中心存放服务地址相关信息(接口地址)



![](https://gitee.com/mingchaohu/blog-image/raw/master/image/3956561052b9dc3909f16f1ff26d01bb.png)

