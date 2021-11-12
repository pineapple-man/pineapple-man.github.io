---
title: yaml
toc: true
clearReading: true
thumbnailImagePosition: bottom
metaAlignment: center
date: 2021-10-30 19:02:27
thumbnailImage: https://gitee.com/mingchaohu/blog-image/raw/master/image/1200px-YAML_Logo.svg.png
categories: 其他
tags: 必备小技能
keywords: yaml
excerpt: 本文主要讲解yaml的语法以及规范
---
<!-- toc -->
![]()

## 1. 简介

Yaml 是一个可读性高的用来表达资料序列的格式，非常适合用来做<font style="color:red;font-weight:bold">以数据为中心</font>的配置文件

:sparkles:Yaml  格式的特点


1. YAML的可读性好
2. YAML和脚本语言的交互性好
3. YAML使用实现语言的数据类型
4. YAML有一个一致的信息模型
5. YAML易于实现
6. YAML可以基于流来处理
7. YAML表达能力强，扩展性好

## 2. 语法

`yaml`文件的书写规则与`json`非常相似

:sparkles:yaml 语法特点

- 使用`kay: value`形式保存数据（冒号后有一个空格）
- 大小写敏感
- 缩进不允许使用`tab`，只允许使用空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- `#`表示注释
- 字符串无需加引号，带引号的字符串有不同的作用
- 使用`---`进行多个文档分隔

```yaml
## \n 原样输出
name1: 'hello \n world'
## \n 表示回车换行符
name2: "hello \n world"
```

:notes:**单双引号**的功能于`shell`中相同

## 3. 数据类型

在`yaml`格式的文件中，只会存在三种数据类型：**字面量、对象**和**数组**

### 3.1 字面量

即字符串常量，是不可再分隔的值

```yaml
key: value
```

### 3.2 对象

对象( Object )是键值对的集合,有两种写法

```yaml
## 单行写法
obj: {key1: value1,key2: value2,key3: value3}
## 多行写法
obj:
    key1: value1
    key2: value2
    key3: value3
```

### 3.3 数组

数组是一组排列的值，有两种写法

```yaml
## 单行写法
list: [v1,v2,v3]
## 多行写法
list:
    - v1
    - v2
    - v3
```

## 附录
[菜鸟教程](https://www.runoob.com/w3cnote/yaml-intro.html)
[wikipedia](https://en.wikipedia.org/wiki/YAML)

