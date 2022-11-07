---
title: Spring 源码阅读（一）Refresh 流程
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: Spring
keywords: Spring Refresh 流程
excerpt: 本文主要记录 spring 中比较重要的 refresh 历程
date: 2022-03-30 00:00:15
thumbnailImage:
---

<!-- toc -->

## 概述

refresh 是 `AbstractApplicationContext` 中的一个方法，负责初始化 ApplicationContext 容器，内部主要会调用 12 个方法，习惯上称这 12 个方法为 12 个步骤，具体这 12 个步骤如下：

1. prepareRefresh
2. obtainFreshBeanFactory
3. prepareBeanFactory
4. postProcessBeanFactory
5. invokeBeanFactoryPostProcessors
6. registerBeanPostProcessors
7. initMessageSource
8. initApplicationEventMulticaster
9. onRefresh
10. registerListeners
11. finishBeanFactoryInitialization
12. finishRefresh

按照每个步骤完成的功能，可以将其分为如下几类：

步骤一：准备环境

步骤二~步骤六：准备 BeanFactory

步骤七~步骤十二：准备 ApplicationContext

步骤十一：初始化 BeanFactory 中非延迟单例 Bean

refresh 在 AbstractApplicationContext 类中进行初始化，这属于模板方法在 Spring 中的应用。

## prepareRefresh

这一步用来创建和准备 Environment 对象，它是 ApplicationContext 的一个成员变量，Environment 对象的作用之一就是为后续`@Value`注解注入提供键值

Environment 分成三个主要部分

systemProperties - 保存 java 环境键值

systemEnvironment - 保存系统环境键值

自定义 PropertySource - 保存自定义键值，例如来自于 \*.properties 文件的键值

## obtainFreshBeanFactory

## 引用
