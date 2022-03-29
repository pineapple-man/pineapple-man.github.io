---
title: MyBatis 动态Sql
toc: true
clearReading: true
thumbnailImage: 'https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/mybatis.jpg'
thumbnailImagePosition: right
metaAlignment: center
categories: 中间件
tags: MyBatis
keywords: 动态sql
excerpt: 动态SQL是MyBatis的特性之一，本文将记录MyBatis中动态Sql的相关操作
date: 2021-11-26 15:41:13
---
<!-- toc -->


## 动态 SQL 概述

动态 SQL是MyBatis强大特性之一，**极大的简化程序员拼装 SQL 的操作**。MyBatis 采用功能强大的基于 OGNL 的表达式来简化操作

:notes:OGNL（ Object Graph Navigation Language ）对象导航图语言，这是一种强大的表达式语言，通过它可以非常方便的来操作对象属性

## if & where 

{% alert success no-icon%}
- If 用于完成简单的判断
- Where 用于解决 SQL 语句中 where 关键字以及条件中第一个 and 或者 or 的问题
{%endalert%}

```xml
<select id="findSomethings"  parameterType="hashmap"   resultType="hashmap">
  select name,age,id from user
  where 1 = 1
  <if test="name != null and name!='' "> and name = #{name}</if>
  <if test="level != null and level!='' "> and level= #{level}</if>
</select>
```

由于存在 `1=1` 这样不太优雅的情况，MyBatis 提供了`<where>`标签
```xml
<select id="selectBySallary" resultType="Employee">
    SELECT * FROM emp
    <where>
        <if test="min != null">
            AND sal >= #{min}
        </if>
        <if test="max != null">
            AND sal <= #{max}
        </if>
    </where>
</select>
```
## trim

:sparkles:Trim 可以在条件判断完的 SQL 语句前后 添加或者去掉指定的字符

`prefix`：添加前缀

`prefixOverrides`去掉前缀

`suffix`：添加后缀

`suffixOverrides`：去掉后缀

## set

{% alert success no-icon%}

类似于 where 的元素，set 元素对应于 SQL 语句中的 SET 子句，它专用于 update 语句，用于包含所需更新的列。set 元素常常和 if 元素联合使用。因为在“**选择性更新**”功能中，有一个最后一个逗号问题
{%endalert%}

```xml
<update id="updateByPrimaryKeySelective" parameterType="Department">
    UPDATE dept
    <set>
        <if test="dname != null"> dname = #{dname}, </if>
        <if test="loc != null"> loc = #{loc}, </if>
    </set>
    WHERE deptno = #{deptno}
</update>
```
:notes:`<small>`更新行为务必要保证更新至少一个属性，否则 MyBatis 更新语句提示 update 语句错误`</small>`
## choose(when & otherwise)
{% alert success no-icon%}

MyBatis 并未提供类似 if-else 元素来处理分支情况，if 元素可出现多次，但它们是并列的判断，而非互斥的判断。`choose-when-otherwise` 元素类似于 Java 中的 switch-case，用于处理多个条件间的互斥判断
{%endalert%}
```xml
<select id="selectBySallary" resultType="Employee">
    SELECT * FROM emp
    <choose>
        <when test="min != null and max != null">
            WHERE sal >= #{min} AND sal <= #{max}
        </when>
        <when test="min != null">
            WHERE sal >= #{min} 
        </when>
        <when test="max != null">
            WHERE sal <= #{max}
        </when>
        <otherwise></otherwise>
    </choose>
</select>
```

## foreach
{% alert success no-icon%}

foreach 元素使用不多，但是当需要构建包含 IN 子句的查询时，则必用到
{%endalert%}

```xml
<select id="selectInDeptnos" resultType="Employee">
    SELECT * FROM emp 
    WHERE deptno IN 
    <foreach collection="list" item="deptno" open="(" separator="," close=")">
        #{deptno}
    </foreach>
</select>
```
foreach 主要用户循环迭代，各个属性含义如下：

- `collection`：要迭代的集合
- `item`：当前从集合中迭代出的元素
- `open`：开始字符
- `separator`：结束字符
- `index`：迭代的是 List 集合，index 表示当前元素的下标；迭代的是 Map 集合，index表示当前元素的key


## sql

sql 标签是用于抽取可重用的 sql 片段， 将相同的， 使用频繁的 SQL 片段抽取出来， 单独定义，方便多次引用（一般在逆向工程中间的比较多）

## 附录

[MyBatis动态SQL中Map参数处理](https://blog.csdn.net/kangkanglou/article/details/93639926)
[MyBatis：动态 SQL](https://www.jianshu.com/p/2ebfa8bf7472)
[mybatis 详解（五）------动态SQL](https://www.cnblogs.com/ysocean/p/7289529.html)