---
title: MySQL 总览
toc: true
clearReading: true
thumbnailImage: "https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/mysql/mysql.jpg"
thumbnailImagePosition: right
metaAlignment: center
categories: 数据库
tags: MySQL
keywords: mysql
excerpt: 本文是 MySQL 总览篇章，沉溺于单个知识点而忘记总揽全局，是我常犯的一个问题，所以希望通过本文的总结，对 MYSQL 数据库有一个全局的掌握
date: 2022-01-25 15:11:46
---

<!-- toc -->

> 最近尚硅谷康师傅出了 MySQL 的视频，虽然很久之前就学过了 MySQL 但是总感觉没有章法，不成体系，所以希望通过本文结合康师傅的视频，整理出一套 MySQL 比较完备的资料。

数据库就像是一颗常青树，不管是普通开发还是首席架构师、CTO 都能够从中汲取足够的技术养料，本文将 MySQL 数据库的学习过程分成两个大篇章：**基础篇**和**高级篇**

## 基础篇

这一部分就是学习的第一个境界：学会使用

### MySQL 安装篇

工欲善其身必先利器，所以要想学习 MySQL 首先将对应的软件安装在本地服务之后再说后话。
{% alert success no-icon %}

{% endalert %}

### DQL

数据库就是用来查询的，所以这是最初学习数据库的核心章节，详细记录如何使用 DQL 语言进行数据的查询

### DDL、DML、DCL 使用篇

作为开发工程师，这一部分其实用的不多，大部分是运维人员或者 DBA 进行操作。这一部分的核心是为 DQL 创建数据源，对数据库中的数据进行声明或者定义操作。

### 其他数据库对象篇

这一部分平时用的其实并不多，主要是为了知识的完备性才补充在这里的，主要介绍视图、存储过程与函数、变量、触发器等概念

### MySQL 8 新特性篇

MySQL 8 与之前有非常大的区别，所以这一小节就是对新特性的了解，学习新特性不仅能够了解到后续 MySQL 的发展趋势，还能了解到之前的设计中是否存在什么弊端

## 高级篇

通过基础篇的学习，已经能够知道如何使用数据库，所以这一阶段是学习的更高阶段：了解 MySQL 的背后原理，从知道怎么做开始逐渐懂得数据库为什么这么做。

### MySQL 架构篇

### 索引及调优篇

### 事务篇

### 日志与备份篇

日志是一个系统的核心，MySQL 的日志更是关键，如果说事务的概念是数据库的核心的话，日志的作用就是事务的基础，众多的`redo.log`以及`undo.log`是数据库能够合理有效保证事务进行的前提。

## 最后的话

非常喜欢康师傅的一句话在笔记中的一句话：{% hl_text red %}具备优秀的思维能力 {% endhl_text %}才是在未来可以迁移的能力，如果只是学习一些命令，则很快会过时，{% hl_text red %} 思维能力和学习能力{% endhl_text %}的提升才是不会变的东西。
