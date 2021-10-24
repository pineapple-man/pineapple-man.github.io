---
title: 控制反转我反转谁了？
toc: true
clearReading: true
metaAlignment: center
date: 2021-10-23 12:01:47
categories: Spring 全家桶
tags: Spring
keywords:
    - Spring
    - IoC
    - 控制反转
excerpt: 本文主要介绍 IoC 思想，以及为什么需要 IoC
---
<!-- toc -->

## 紧耦合

编码过程中，两个甚至多个类通过彼此的合作来实现业务逻辑，此时这些类不可避免的将**产生联系**

:notes:如果两个类直接交互，代码的耦合度就会增加，提高维护的成本

### 案例

假如你是班长，如今班级需要打扫卫生，所以你需要安排一些学生去打扫卫生

```java
//打扫卫生的学生
public class Student {
 public void clean() {
  System.out.println("我是学生，我在打扫卫生");
 }
}
//班长类
public class Monitor {
 public void command() {
  new Student().clean();
 }
}
```

```java
public class Test {

 public static void main(String[] args) {
  Monitor monitor = new Monitor();
  monitor.command();
 }

}
```

### 分析

Monitor 类的 command方法中使用 new 关键字创建了一个 Student 类的对象，**这种代码的耦合度就很高，维护起来的成本就很高**

如果，某一天，学校体贴学生，让专门的清洁工来打扫卫生，此时班长需要更改其内部的代码，于是 Monitor 类就不得不重构，于是代码变成了这样：

```java
public class Worker {
    public void clean() {
        System.out.println("我是清洁工，我在扫地");
    }
}
public class Monitor {
    public void command() {
        new Student().clean();
    }

    public void command1() {
        new Worker().clean();
    }
}
```

假如需求再一次改变，想用其他的方式进行打扫卫生，班长没准要下命令给扫地机器人去打扫卫生了，这样下去的话，Monitor 类就非常不符合设计原则，**对扩展开放，对修改关闭**

## 控制反转(IoC)

需要自己不断更改已经完成的代码这样太复杂了，班长想：不如把这个安排扫地的差事交给劳动委员吧，劳动委员负责安排人员去执行班长下达的扫地命令，这样我就轻松多了

```java
//可以扫地接口
public interface CleanAble {
    void clean();
}
//学生类的代码修改如下
public class Student implements CleanAble {

    @Override
    public void clean() {
        System.out.println("我是学生，我在打扫卫生");        
    }

    public boolean canClean() {
        // 根据学校的政策判断是否需要学生打扫卫生
        return false;
    }
}
//清洁工类的代码修改如下
public class Worker implements CleanAble {

    @Override
    public void clean() {
        System.out.println("我是清洁工，我在扫地");        
    }
}
//班长类代码修改如下
public class Monitor {
    public static CleanAble getCleaner() {
        Student student = new Student();
        if (student.canClean()) {
            return new Worker();
        }
        return student;
    }
}
//班长代码修改如下
public class Monitor {
    public void command() {
        Monitor.getCleaner().clean();
    }
}

```

班长是不是省心多了，他只管下命令，该叫谁去扫地由劳动委员去负责

:sparkles:这种减少班长控制逻辑的方法称为**控制反转（Inversion of Control，缩写为 IoC）**，它不是一种技术，而是一种思想，能够指导我们设计出松耦合的程序

:thinking:谁的控制被反转了？

- 在紧耦合的情况下，班长需要自己通过 new 关键字创建依赖的对象
- 通过IoC之后，班长制行为被发转给了劳动委员，劳动委员代替班长指派人员去扫地，班长知道劳动委员会安排人去扫地就可以了

:notes:通过IoC，可以将原本的主控制类的繁琐判断操作转化为简单的使用操作

## 依赖注入

:sparkles:依赖注入（Dependency Injection，简称 DI）是实现控制反转的主要方式

> **依赖注入**（dependency injection）的意思为：给予调用方它所需要的事物，主要包括两部分：**依赖**和**注入**
>
> 依赖：指可被方法调用的事物
> 注入：指将**依赖**传递给调用方的过程，**注入**之后，调用方才会调用该**依赖**

:notes:**传递依赖给调用方，而不是让调用方直接获得依赖**，这个是该设计的根本需求

:sparkles:不同角度下的**调用方**和**依赖**

- 在编程语言角度下，**调用方**为对象和类，**依赖**为变量
- 在提供服务的角度下，**调用方**为客户端，**依赖**为服务

:notebook:简单的理解就是：**将创建对象的任务转移给其他 class，并直接使用依赖项的过程，被称为依赖项注入**

### 三种类型的依赖注入

:sparkles:三种类型的依赖注入方式

- **构造函数注入**：依赖关系是通过 class 构造器提供的
- **setter 注入**：注入程序用客户端的 setter 方法注入依赖项
- **接口注入**：依赖项提供了一个注入方法，该方法将把依赖项注入到传递给它的任何客户端中。客户端必须实现一个接口，该接口的 setter 方法接收依赖

:thinking:依赖注入有哪些作用

- 创建对象
- 知道哪些类需要那些对象
- 并提供所有这些对象

如果对象有任何更改，则依赖注入会对其进行调查，不会影响到使用这些对象的类。这样，如果将来对象发生变化，依赖注入负责为类提供正确的对象，使用依赖的类并不会发生任何的变化

### IoC的背后——依赖注入

一个类不应静态配置其依赖项（类应该依赖于抽象，而不是依赖于具体），而应由其他一些类从外部进行配置

:sparkles:根据这些原则，一个类应该专注于履行其职责，而不是创建履行这些职责所需的对象。 这就是**依赖注入**发挥作用的地方：它为类提供了必需的对象

### 依赖注入优劣分析

:+1:依赖注入的优势

- 帮助进行单元测试
- 由于依赖关系的初始化是由注入器组件完成的，因此减少了样板代码
- 扩展应用程序变得更加容易
- 帮助实现松耦合

:persevere:依赖注入的劣势

- 学习起来有点复杂，如果使用过度会导致管理问题和其他问题
- 许多编译时错误被推送到运行时
- 依赖注入框架是通过反射或动态编程实现的。这可能会妨碍 IDE 自动化的使用，例如**查找引用**，**显示调用层次结构**和**安全重构**

## 附录

[Java：控制反转（IoC）与依赖注入（DI）](https://juejin.cn/post/6844903907861364750)
[依赖注入是什么？如何使用它？](https://chinese.freecodecamp.org/news/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it/)
