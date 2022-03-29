---
title: Eureka 踩坑记录
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: SpringCloud
keywords: 服务注册中心
excerpt: 本文主要记录 Eureka 组件使用过程中遇到的问题
date: 2022-1-11 18:00:24
thumbnailImage:
---
<!-- toc -->
## 概述
本文主要记录在使用 eureka 的过程中，踩过的一些坑
## eureka中registered-replicas为空和eureka集群搭建会遇到的问题
主要是因为使用了 `localhost` 作为 eureka 地址的原因，将 localhost 换成 `127.0.0.1` 这个问题就解决了[^1]

## eureka启动I/O error on POST request for "http://localhost:9411/api/v2/spans"
pom 依赖中含有 zipkin 依赖，没有配置 zipkin-server, 默认提交 9411，**删除这个依赖就可以解决问题**[^2]

## 附录
[^1]:https://www.pianshen.com/article/52851945773/
[^2]:https://gitee.com/log4j/pig/issues/I161YA#note_2119281