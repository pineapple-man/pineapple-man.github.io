---
title: Feign
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-01-31 01:58:34
thumbnailImage:
categories: Java
tags: SpringCloud
keywords: OpenFeign
excerpt: 什么是 OpenFeign? 为什么要用 OpenFeign?
---

<!-- toc -->

## 概述

:question: 什么是 Feign?

{% alert success no-icon %}

Feign 是一个声明式 WebService 客户端。使用 Feign 能让编写 Web Service 客户端更加简单。它的使用方法是定义一个服务接口然后在上面添加注解。Feign 也支持可拔插式的编码器和解码器。Spring Cloud 对 Feign 进行了封装，使其支持了 Spring MVC 标准注解和 `HttpMessageConverters`。Feign 可以与 Eureka 和 Ribbon 组合使用以实现负载均衡的效果。

{% endalert %}

:dart: Feign 能干什么？

{% alert success no-icon %}

Feign 的目的是**让开发人员编写 Java Http 客户端变得更加容易**；

{% endalert %}

前面在使用 Ribbon+RestTemplate 时，利用 RestTemplate 对 http 请求的封装处理，形成了一套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign 在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。

在 Feign 的实现下，如今只需创建一个接口并使用注解的方式来配置它(以前是 Dao 接口上面标注 Mapper 注解,现在是一个微服务接口上面标注一个 Feign 注解即可)，即可完成对服务提供方的接口绑定，简化了开发复杂度。

:vs: Feign 与 OpenFeign 的区别？

{% alert success no-icon %}

- Feign 是 Spring Cloud 组件中的一个轻量级 RESTful 的 HTTP 服务客户端，Feign 内置了 Ribbon 用来做客户端负载均衡，去调用服务注册中心的服务。Feign 的使用方式是：使用 Feign 的注解定义接口，调用这个接口，就可以调用服务注册中心的服务，想要使用 Feign，就需要引入对应的依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
```

- OpenFeign 是 Spring Cloud 在 Feign 的基础上支持了 SpringMVC 的注解，如`@RequesMapping`等等。OpenFeign 的`@Feignclient`可以解析 SpringMVC 的`@RequestMapping`注解下的接口，并通过动态代理的方式产生实现类，实现类中做负载均衡并调用其他服务。如果想要在项目中使用 OpenFeign ，同样需要引入下列依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

{% endalert %}

简而言之，Feign 就是一个便捷的服务调用组件，使用它不再需要开发人员自己手动使用`RestTemplate`进行远程服务调用

## OpenFeign 服务调用

使用 OpenFeign 只需要两步就可以：提供者提供的调用接口以及消费者在接口上标注`@FeignClient`注解即可

## 附录

[SpringCloud 官网](https://www.springcloud.cc/spring-cloud-greenwich.html#_spring_cloud_openfeign)
[springCloud-服务调用](https://blog.csdn.net/MOKEXFDGH/article/details/107287927)
