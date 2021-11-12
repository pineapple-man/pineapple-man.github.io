---
title: MyBatis——动态Sql
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: 中间件
tags: MyBatis
keywords: 动态sql
excerpt: 动态SQL是MyBatis的特性之一，本文将记录MyBatis中动态Sql的相关操作
---

# 动态SQL

:sparkles:MyBatis 动态 SQL 特性：

- 动态 SQL是MyBatis强大特性之一，极大的简化程序员拼装SQL的操作
- 动态 SQL 元素和使用 JSTL 或其他类似基于 XML 的文本处理器相似
- MyBatis 采用功能强大的基于 OGNL 的表达式来简化操作

:notes:OGNL（ Object Graph Navigation Language ）对象图导演语言，这是一种强大的表达式语言，通过它可以非常方便的来操作对象属性

# if & where 

:sparkles:If 用于完成简单的判断

:sparkles:Where 用于解决 SQL 语句中 where 关键字以及条件中第一个 and 或者 or 的问题

# trim

:sparkles:Trim 可以在条件判断完的 SQL 语句前后 添加或者去掉指定的字符

`prefix`：添加前缀

`prefixOverrides`去掉前缀

`suffix`：添加后缀

`suffixOverrides`：去掉后缀

# set

set 主要是用于解决修改操作中 SQL 语句中可能多出逗号的问题

# choose(when & otherwise)

choose 主要是用于分支判断， 类似于 java 中的 switch case,只会满足所有分支中的一个

# foreach

foreach 主要用户循环迭代

`collection`：要迭代的集合

`item`：当前从集合中迭代出的元素

`open`：开始字符

`separator`：结束字符

`index`：

- 迭代的是 List 集合，index 表示当前元素的下标
- 迭代的是 Map 集合，index表示当前元素的key

# sql

sql 标签是用于抽取可重用的 sql 片段， 将相同的， 使用频繁的 SQL 片段抽取出来， 单
独定义， 方便多次引用



https://blog.csdn.net/kangkanglou/article/details/93639926
https://www.jianshu.com/p/2ebfa8bf7472
https://www.cnblogs.com/ysocean/p/7289529.html