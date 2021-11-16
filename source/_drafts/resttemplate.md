---
title: RestTemplate
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: Java
tags: Spring
keywords: RestTemplate
excerpt: 本文主要记录在 Spring 中的 RestTemplate 的使用方式
---
Spring RestTemplate 可以用来构建 Spring REST 客户端，访问其他服务提供的 REST API

:notebook:简而言之，Spring RestTemplate 用来快捷访问其他 RESTful 接口
## 概述
{% blockquote "spring docs"  "Spring doc"%}

Spring 文档建议使用非阻塞、反应式的 `WebClient` 对象而不再是 `RestTemplate`，因为 `WebClient` 为同步、异步和流场景提供了有效支持，并且 `RestTemplate` 将在未来版本中弃用
{% endblockquote%}

:question:什么是 `RestTemplate`？
{% alert success no-icon%}
spring框架提供的RestTemplate类可用于在应用中调用rest服务，它简化了与http服务的通信方式，统一了RESTful的标准，封装了http链接， 我们只需要传入url及返回值类型即可。相较于之前常用的HttpClient，RestTemplate是一种更优雅的调用RESTful服务的方式。
{%endalert%}
在Spring应用程序中访问第三方REST服务与使用Spring RestTemplate类有关。RestTemplate类的设计原则与许多其他Spring 模板类(例如JdbcTemplate、JmsTemplate)相同，为执行复杂任务提供了一种具有默认行为的简化方法（模板设计模式）。

RestTemplate默认依赖JDK提供http连接的能力（HttpURLConnection），如果有需要的话也可以通过setRequestFactory方法替换为例如 Apache HttpComponents、Netty或OkHttp等其它HTTP library

考虑到RestTemplate类是为调用REST服务而设计的，因此它的主要方法与REST的基础紧密相连就不足为奇了，后者是HTTP协议的方法:HEAD、GET、POST、PUT、DELETE和OPTIONS。例如，RestTemplate类具有headForHeaders()、getForObject()、postForObject()、put()和delete()等方法

## 实现
[最新api地址](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html)

RestTemplate包含以下几个部分：

- `HttpMessageConverter` 对象转换器
- `ClientHttpRequestFactory` 默认是 JDK 的 `HttpURLConnection`
- `ResponseErrorHandler` 异常处理
- `ClientHttpRequestInterceptor` 请求拦截器

```java
@Bean
public RestTemplate restTemplate(RestTemplateBuilder builder) {
 
    return builder
            .setConnectTimeout(Duration.ofMillis(3000))
            .setReadTimeout(Duration.ofMillis(3000))
            .build();
}
```

## 附录

[RestTemplate用法说明](https://www.jianshu.com/p/2a59bb937d21)

[Spring之RestTemplate使用小结](https://juejin.cn/post/6844903656165212174)

[RestTemplate使用教程](https://www.cnblogs.com/f-anything/p/10084215.html)

[Spring RestTemplate](https://howtodoinjava.com/spring-boot2/resttemplate/spring-restful-client-resttemplate-example/)