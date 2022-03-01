---
title: Spring 源码分析（一）
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: Java
tags: Spring
keywords: Spring
excerpt: Spring 源码分析第一节，主要记录如何搭建 Spring 源码分析环境，并认识 Spring 中几个比较重要的接口
---

## 环境搭建

Spring 项目使用 Gradle 构建，因此想要能够正常构建 Spring 项目，最好下载一个自己的 Gradle（虽然 IDEA 里面会自带）具体如何导入，配置基础环境我这里也不过多阐述了，可以参考这几篇博客[^1][^2][^3]

## Spring 核心架构

Spring 是一个整合框架，包含有非常多的组件，详细的组件架构图如下：

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/spring/spring核心架构.jpg %}

{% alert success no-icon %}

所有的架构图都有这样一个特性，上层的使用依赖下层的实现，所以从下向上可以方便的进行源码的阅读，所以阅读源码也是从最基础的功能开始，不断的扩充。也可以按照功能来逐个的选择元吗框架进行阅读

{% endalert %}

## 核心组件接口

Java 作为一门面向接口的语言，Spring 的编写符合这一点，在框架中，都是现有一系列的接口定义，随后才是衍生出众多的接口实现类。所以，先看看 Spring 中比较重要的几个接口

### `Resource`

{% blockquote  "Spring源码中对 Resources 的注释"  %}

Interface for a resource descriptor that abstract from the actual type of underlying resouce,such as file or class path resource.

{% endblockquote%}

{% alert success no-icon %}

Spring 将我们的配置文件等抽象为 `Resources`，随后就可以在 Sprig 系统中可以使用 `Resources` 类进行众多操作。Spring 中 Resources 的继承体系如下：

{% endalert %}
{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/spring/Resource.png %}

### `ResourceLoader`
{% blockquote "Spring 源码中对 ResourceLoader 的注释"  %}

Strategy interface for loading resources (e.g., class path or file system resources). An  `org.springframework.context.ApplicationContext` is required to provide this functionality plus extended `org.springframework.core.io.support.ResourcePatternResolver` support.  

`DefaultResourceLoader` is a standalone implementation that is usable outside an ApplicationContext and is also used by `ResourceEditor`.

Bean properties of type Resource and Resource[] can be populated from Strings when running in an ApplicationContext, using the particular context's resource loading strategy.(如果使用 spring 中的applicationcontext 就能够通过名称进行自动装配)

{% endblockquote%}
:question: 既然说明是策略模式，那么策略模式到底体现在哪里？

{% alert success no-icon %}

Spring 将众多资源抽象为 `Resource` 后，必然需要将这些资源读入系统，此时定义的组件就是`ResourceLoader` 接口，通过`ResourceLoader` 接口可以将众多不同类型的资源读入到 Spring 系统中。

{% endalert %}

此接口的作用即然是读取资源，那么其中最重要的方法当然就是根据路径找到对应的资源了
{% codeblock ResourceLoader lang:java %}
public interface ResourceLoader {
    ///......省略
    /** 核心方法，根据 location 得到对应的资源 **/
    Resource getResource(String location);
    //......省略
}
{% endcodeblock %}


ResouceLoader 的继承体系如下，可以看到最初常用的 `ClassPathXmlApplicationContext` 就属于一种特殊的实现方式：
{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/spring/ResourceLoader.png %}

### `BeanFactory`

the point of this approach is that the BeanFactory is a central registry of application components, and centralizes configuration of application components

Note that it is generally better to rely on Dependency Injection ("push" configuration) to configure application objects through setters or constructors, rather than use any form of "pull" configuration like a BeanFactory lookup. Spring's Dependency Injection functionality is implemented using this BeanFactory interface and its subinterfaces.



![](https://gitee.com/mingchaohu/blog-image/raw/master/image/spring/BeanFactory.png)

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/spring/BeanFactoryAll.png)



HierachicalBeanFactory 定义父子工厂（父子容器）

ListableBeanFactory 的实现是 DefaultListableBeanFactory，保存了 ioc 容器中的核心信息

AutowireCapaableBeanFactory 提供自动装配能力

AnnotationApplicationContext 组合了档案馆，它拥有自动装配能力



:question: ApplicationContext 和 BeanFactory 有什么区别？



### `BeanDefinition`



### `BeanDefinitionReader`



### `BeanDefinitionRegistry`



### `ApplicationContext`



### `Aware`



### `BeanFactoryPostProcessor`



### `InitializingBean`



### `DisposableBean`



### `BeanPostProcessor`



## 核心容器源码-配置文件解析流程



Spring 整个工厂的启动称之为 `refresh` 刷新工厂

`DefaultListableBeanFactory` 是 Spring 的档案馆，报错所有的 Bean 信息

Spring 底层源码中使用最多的就是模版模式

Spring 的底层通过后置增强机制，完成很多功能



这个功能 Spring 在启动的时候注入了什么组件？

这个功能牵扯到的组件在什么位置被什么后置增强器增强成了什么样子？

## 附录

[^1]:https://blog.csdn.net/KKALL1314/article/details/113450506:
[^2]:https://www.cnblogs.com/mazhichu/p/13163979.html
[^3]:https://blog.csdn.net/zxs9999/article/details/113511447