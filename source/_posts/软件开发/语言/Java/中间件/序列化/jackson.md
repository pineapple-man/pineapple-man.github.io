---
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2021-11-13 00:26:52
title: Jackson 常用操作
thumbnailImage:
categories: 中间件
tags: Web
keywords: jackson
excerpt: 本文主要讲解 Spring 中的序列化工具——Jackson
---
<!-- toc -->

## 概述





## 常见问题

### `No serializer found for class 类名 and no properties discovered to create BeanSerializer`

使用`@RestController`注解，但是前台却获取不到返回对象，并且触发上述异常

:book:需要序列化的实体类没有`getter`和`setter`方法，或者对应的方法并不是`public`的，所以触发了序列化异常

## 附录

https://www.cnblogs.com/bolingcavalry/p/14360209.html

https://blog.csdn.net/weixin_44130081/article/details/89678450

[jackson序列化错误](https://cloud.tencent.com/developer/article/1668655)

https://blog.csdn.net/yu870646595/article/details/78523402
