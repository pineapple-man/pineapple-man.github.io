---
title: UML（一）类图
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 软件开发
tags: 设计模式
keywords: UML
excerpt: 本文主要记录 UML 中类图绘制的相关细节
---
<!-- toc -->
## 概念
{% alert success no-icon %}

类图（Class Diagrame）是描述类、接口、协作以及它们之间关系的图，用来显示系统中各个类的静态结构，类图包含7个元素：类、接口、协作、依赖关系、泛化关系、实现关系以及关联关系。

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml-类图概述.png %}

:question: 为什么非要使用 UML 类图？

{% alert info no-icon %}

通过 UML 类图可以得到软件开发人员的共同语言，并且能够快速了解软件中设计的细节

{% endalert %}

:question: UML 能够对哪些事物进行建模？
{% alert info no-icon %}

- 对系统的词汇建模（建立抽象系统词汇，如班级、学生）；
- 对简单协作建模（将系统词汇中是事物协同工作的方式可视化和详述，如班级和学生的关系表示）；
- 对逻辑数据库模式建模

{% endalert %}

:question: UML 类图都有哪些元素构成？

## UML 类图的组成元素
{% alert success no-icon %}

在类图中，类用矩形来表示，分为3个部分：名称部分（Name）、属性部分（Attribute）和操作部分（Operation，也可称作方法）

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml类图元素.png %}

### 类名称
{% alert success no-icon %}

类的名称是一个文本串，分为简单名称和路径名称，简单名（single name）即，单独的名称不含冒号；路径名（path name）即用类所在的包的名称作为前缀

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml类名称.png %}

### 类属性
{% alert success no-icon %}

描述类在软件系统中代表的事物所具备的特性，UML 中类属性的语法：`[可见性] 属性名 [：类型] [=初始值] [{属性字符串}]`，如:`- name: string`，其中 `[]` 中的部分是可选的。

{% endalert %}
{% alert warning no-icon %}

类属性的可见性有以下三种：共有（Public)、私有（Private)和受保护（Protected)，与 Java 中的可见性相同，具体的使用规则如下：

- 共有类型可以被外部查看和使用，用 `+` 表示；
- 私有类型即不可以从其他类中访问该属性，用 `-` 表示；
- 受保护类型常与泛化和特化一起使用，用 `#` 表示。
- 如果没有符号，表示没有定义该属性的可见性

{% endalert %}

{% alert warning no-icon %}

类属性中的属性名由描述所属类的特性的名词或名词短语组成，属性名采用驼峰原则进行命名

{% endalert %}
{% alert warning no-icon %}

类属性典型的属性类型有：整数型、布尔型、实型和枚举类型。当一个类的属性被完整定义后，任何一个对象的状态都由这些属性的特定值所决定

{% endalert %}

- 类属性中的初始值只是为了保证系统的完整性，为用户提供易用性，不常使用；
- 类属性中的属性字符串是关于属性的其他信息，通常是属性的约束信息，也不常使用

### 操作
{% alert success no-icon %}

类的操作是对类的对象所能做的事务的抽象，相当于服务的实现。UML 中类操作的语法：`[可见性] 操作名 [ (参数表）] [: 返回类型] [{属性字符串}]`，如`+ search( ): Person`，其中 `[]` 中的部分是可选的。

{% endalert %}

{% alert warning no-icon %}

操作的可见性包括共有（Public）、私有(Private)、受保护（Proteted）和包内公有（Package）4种，与类属性稍有不同，具体的细节如下：

- 其中公有类型即只要调用对象能访问操作所在的包，就可调用该操作，用 `+` 表示；
- 私有类型即只有属于同一个类的对象才可以调用，用 `-` 表示；
- 受保护类型即只有子类的对象才可以调动父类，用 `#` 表示；
- 包内公有类型即只有在同一个包里的对象才可以调用，用 `~` 表示
{% endalert %}

{% alert warning no-icon %}

操作名即方法名称，同样采用驼峰原则进行命名

{% endalert %}

{% alert warning no-icon %}

操作中的参数表，指一些按顺序排列的属性定义了操作的输入。定义方式采取「 名称：类型 」进行，如果有多个参数，则用逗号隔开

{% endalert %}

操作的返回值类型也和 Java 中的使用方式相同

## 接口
{% alert success no-icon %}

接口是指类或组件所提供的、可以完成特定功能的一组操作的集合。接口描述了类或组件的对外的、可见的动作，通常一个类实现一个或多个接口。接口就是为类制定了一种规范，是类与类之间的一种约束和协定

{% endalert %}

## 关系

关系是 UML 类图的关键，接下来主要介绍 UML 类图中抽象出来的集中关系

### 依赖（Dependency）关系

{% alert success no-icon %}

依赖关系表示某一类元以某种形式依赖于其他类元

{% endalert %}
依赖关系表示了下图这样的关系，一个元素（提供者）的更改会影响其他元素（客户），即，**客户以某种形式依赖于提供者**

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml类图依赖关系.webp %}

### 泛化（Generalization）关系

{% alert success no-icon %}

泛化关系对应到编程中就是继承关系

{% endalert %}

泛化关系表示一种存在于一般元素和特殊元素之间的分级关系，描述了 `is a kind of`（是……的一种）的关系，如汽车是交通工具的一种，在类中一般元素称为超类或父类，特殊元素称为子类。

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml类图泛化关系.webp %}

### 关联(Association)关系
{% alert success no-icon %}

关联关系表示一组具有共同结构特征、行为特征、关系和语义的链接，是一种结构关系，指明一个事物的对象与另一个事物的对象间的关系，通常两者相互依赖
{% endalert %}

如学生和大学的关系，学生在大学里学习，大学又包括了很多学生，所以可以在学生和大学之间建立关联关系。


{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml类图关联关系.webp %}

关联的多重性规定了一个类的多少个实例能与另一个类的单个实例建立关联。关联的多重性有以下几种情况：**一对一关联**、**一对多关联**、**规定数值关联**、**可选关联**和**多对多关联**

关联关系默认不强调方向，表示对象间相互知道，如果特别强调方向，表示 A 知道 B，但 B 不知道 A ，加上方向，表示关联的之间的可见性

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml_association.jpg %}

{% alert info no-icon %}

在代码中，关联对象通常是以成员变量的形式实现的；

{% endalert %}

### 聚合（Aggregation）关系
{% alert success no-icon %}

是一种特殊形式的关联关系。表示**整体与部分关系的关联**，简单来说，就是关联关系中的一组元素组成了一个更大、更复杂的单元,描述了 `has a` 的关系。如大学和学院，大学是由很多个学院组成的，因此两者之间是聚合关系。

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml类图聚合关系.png %}

聚合关系用一条带空心菱形箭头的直线表示，如下图表示 A 聚合到 B 上，或者说 B 由 A 组成；

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml_aggregation.jpg %}

{% alert info no-icon %}

聚合关系用于表示实体对象之间的关系，表示整体由部分构成的语义；例如一个部门由多个员工组成。聚合关系中整体和部分不是强依赖关系，即使整体不存在了，部分仍然存在；例如：部门撤销了，人员不会消失，他们依然存在

{% endalert %}

### 组合（composition）关系
{% alert info no-icon %}

组合关系表示整体有部分构成的语义，但组合关系是一种强依赖的特殊聚合关系，**如果整体不存在了，则部分也不存在了**

{% endalert %}

组合关系用一条带实心菱形箭头直线表示，如下图表示 A 组成 B，或者 B 由 A 组成；

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml_composition.jpg %}

### 实现（Realization）关系
{% alert success no-icon %}

表示定义和其实现之间的关系，通常用来将一种模型元素和另一种模型元素连接起来，比如类和接口。 

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml类图实现关系.webp %}

### 连接关系图形总结

在 UML 类图中主要存在以下几种关系：

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/designPattern/uml类图连接关系总结.webp %}

## 附录

[人人都是产品经理](http://www.woshipm.com/pd/2593231.html)
[看懂UML类图和时序图](https://design-patterns.readthedocs.io/zh_CN/latest/read_uml.html)

