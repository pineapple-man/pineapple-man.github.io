---
title: SpringCloud 概述
toc: true
clearReading: true
thumbnailImagePosition: bottom
metaAlignment: center
categories: Java
tags: SpringCloud
keywords: SpringCloud
excerpt: 作为 Spring 家族中的重量级角色—— Spring Cloud 到底是做什么的？本文是 Spring Cloud 学习的入口
date: 2021-11-04 22:59:53
thumbnailImage: https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/springcloudAlibaba.jpg
---

<!-- toc -->

## 概述

:thinking:Spring Cloud 是什么？

{% alert success no-icon%}

Spring Cloud 是分布式微服务架构的一站式解决方案，是多种微服务架构落地技术的集合体，俗称<font style="color:red;font-weight:bold">微服务全家桶</font>

{% endalert %}

{% image fancybox fig-100 center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/eeb48f15799b978e45ed980172c9f06e.png %}

Spring Cloud provides tools for developers to quickly build some of the common patterns in distributed systems.

:sparkles:Spring Cloud Features

- Distributed/versioned configuration
- Service registration and discovery
- Routing
- Service-to-service calls
- Load balancing
- Circuit Breakers
- Global locks
- Leadership election and cluster state
- Distributed messaging

> no microservice architecture is complete without Spring Cloud ‒ easing administration and boosting your fault-tolerance.

## Spring Cloud 涉及的维度

{% image fancybox fig-100 center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/SpringCloud.png %}

## SpringCloud 组件一览表

{% image fancybox fig-100 center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/Cloud升级.png %}

## 服务治理

{% alert success no-icon%}

在传统的 RPC 远程调用框架中，每个服务于服务之间依赖关系十分复杂，管理起来比较繁琐，所以需要使用一种智能的服务治理（管理）软件，来管理服务于服务之间依赖关系，实现服务调用、服务发现、服务注册、负载和容错等
{%endalert%}
在 Spring Cloud 中可以选择使用 `Eureka`,`Zookeeper`,`Consul`和`Nacos` 其中 `Eureka`已经停更，所以目前社区新星 `Nacos` 关注度还挺高的，不过一些旧的项目仍然在使用 `Eureka` 和 `Zookeeper`


## 学习资料

[Spring Cloud 官方文档](https://cloud.spring.io/spring-cloud-static/Hoxton.SR1/reference/htmlsingle/)
[Spring Cloud 中文文档](https://www.bookstack.cn/read/spring-cloud-docs/docs-index.md)
[Spring Boot 官方文档](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/htmlsingle/)
