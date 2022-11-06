---
title: Spring RestTemplate 详解
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: Spring
keywords: RestTemplate
excerpt: 本文主要记录在 Spring 中的 RestTemplate 的使用方式
date: 2022-01-12 12:31:21
thumbnailImage:
---

<!-- toc -->

Spring RestTemplate 可以用来构建 Spring REST 客户端，访问其他服务提供的 REST API

## 概述

{% blockquote "spring docs"  "Spring doc"%}

Spring 文档建议使用非阻塞、反应式的 `WebClient` 对象而不再是 `RestTemplate`，因为 `WebClient` 为同步、异步和流场景提供了有效支持，并且 `RestTemplate` 将在未来版本中弃用

{% endblockquote%}
{% alert success no-icon %}

但是以上的原因并不影响我们使用，因为学会一者之后就会触类旁通！

{% endalert %}

:question:什么是 `RestTemplate`？
{% alert success no-icon%}

spring 框架提供的 RestTemplate 类可用于在应用中调用 rest 服务，它简化了与 http 服务的通信方式，统一了 RESTful 的标准，封装了 http 链接， 我们只需要传入 url 及返回值类型即可。相较于之前常用的 HttpClient，RestTemplate 是一种更优雅的调用 RESTful 服务的方式

{%endalert%}

在 Spring 应用程序中访问第三方 REST 服务与使用 Spring RestTemplate 类有关。RestTemplate 类的设计原则与许多其他 Spring 模板类(例如：JdbcTemplate、JmsTemplate)相同，为执行复杂任务提供了一种具有默认行为的简化方法「模板设计模式」

RestTemplate 默认依赖 JDK 提供 http 连接的能力 `HttpURLConnection`，如果有需要的话也可以通过 `setRequestFactory` 方法替换为例如 `Apache HttpComponents`、`Netty`或 `OkHttp` 等其它 HTTP library

{% alert info no-icon %}

RestTemplate 包含以下几个部分

{% endalert %}
|组件|含义|
| :----------:|:----------:|
|`HttpMessageConverter`|对象转换器|
|`ClientHttpRequestFactory` |默认是 JDK 的 `HttpURLConnection`|
|`ResponseErrorHandler`|异常处理|
|`ClientHttpRequestInterceptor`| 请求拦截器|
{% alert info no-icon %}

RestTemplate 具体的继承体系如下：

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/spring/RestTemplate.png %}

## 构建 `RestTemplate`

想要使用`RestTemplate`需要在 Spring IoC 容器中创建`RestTemplate`，这里给出了几种创建方式

### 使用 `RestTemplateBuilder`

{% codeblock "基于 RestTemplateBuilder 的配置" lang:java %}
@Bean
public RestTemplate restTemplate(RestTemplateBuilder builder) {

    return builder
            .setConnectTimeout(Duration.ofMillis(3000))
            .setReadTimeout(Duration.ofMillis(3000))
            .build();

}
{% endcodeblock %}

### 使用 `SimpleClientHttpRequestFactory`

{% codeblock "基于 SimpleClientHttpRequestFactory 的配置" lang:java %}
@Bean
public RestTemplate restTemplate() {

    var factory = new SimpleClientHttpRequestFactory();
    factory.setConnectTimeout(3000);
    factory.setReadTimeout(3000);
    return new RestTemplate(factory);

}
{% endcodeblock %}

### 使用 Apache `HTTPClient`（推荐）

{% codeblock "基于 Apache HttpClient 的配置" lang:java %}
@Autowired
CloseableHttpClient httpClient;

@Value("${api.host.baseurl}") //配置在 application.yaml 中
private String apiHost;

@Bean
public RestTemplate restTemplate() {
RestTemplate restTemplate = new RestTemplate(clientHttpRequestFactory());
restTemplate.setUriTemplateHandler(new DefaultUriBuilderFactory(apiHost));
return restTemplate;
}

@Bean
@ConditionalOnMissingBean
public HttpComponentsClientHttpRequestFactory clientHttpRequestFactory() {
HttpComponentsClientHttpRequestFactory clientHttpRequestFactory
= new HttpComponentsClientHttpRequestFactory();
clientHttpRequestFactory.setHttpClient(httpClient);
return clientHttpRequestFactory;
}
{% endcodeblock %}

## RestTemplate 常用操作

```java
// get 请求
public <T> T getForObject();
public <T> ResponseEntity<T> getForEntity();

// head 请求
public HttpHeaders headForHeaders();

// post 请求
public URI postForLocation();
public <T> T postForObject();
public <T> ResponseEntity<T> postForEntity();

// put 请求
public void put();

// patch
public <T> T patchForObject

// delete
public void delete()

// options
public Set<HttpMethod> optionsForAllow

// exchange
public <T> ResponseEntity<T> exchange()
```

上面提供的几个接口，基本是 Http 提供的几种访问方式的对应，如果想查看更加全面的 API 文档，可以访问[官方 API 网址](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html)

### RestTemplate – GET 请求

执行 `GET` 操作的可用方法有

|                           API 方法                           |                                                       含义                                                       |
| :----------------------------------------------------------: | :--------------------------------------------------------------------------------------------------------------: |
|                 getForObject(url, classType)                 |                           通过对 URL 执行 GET 来检索表示。响应（如果有）给定类型并返回                           |
|               getForEntity(url, responseType)                |                               通过对 URL 执行 GET 来检索作为*ResponseEntity*的表示                               |
|    exchange(url, httpMethod, requestEntity, responseType)    |                             执行指定`RequestEntity`并将响应作为*ResponseEntity*返回                              |
| execute(url, httpMethod, requestCallback, responseExtractor) | 使用 HTTPMethod 访问给定的 URI 模板，设置相应的请求回调 RequestCallback，并且使用 ResponseExtractor 检查响应信息 |

:question:可以使用的有两种方式 `getForObject` 和 `getForEntity`，那么这两种有什么区别？
{% alert warning no-icon %}

- 从接口的签名上，可以看出一个是直接返回预期的对象，一个则是将对象包装到 `ResponseEntity` 封装类中
- 如果只关心返回结果，直接用 `GetForObject` 即可
- 如果除了返回的实体内容之外，还需要获取返回的 header 等信息，则可以使用 `getForEntity`

{% endalert %}

#### getForObject 方式

具体方法调用原型如下：

```java
public <T> T getForObject(String url, Class<T> responseType, Object... uriVariables) throws RestClientException ;

public <T> T getForObject(String url, Class<T> responseType, Map<String, ?> uriVariables) throws RestClientException ;

public <T> T getForObject(URI url, Class<T> responseType) throws RestClientException;
```

测试代码：

```java
public class RestTestmplateTest {
    private RestTemplate restTemplate;

    @Before
    public void init() {
        restTemplate = new RestTemplate();
    }

    @lombok.Data
    static class InnerRes {
        private Status status;
        private Data result;
    }

    @lombok.Data
    static class Status {
        int code;
        String msg;
    }

    @lombok.Data
    static class Data {
        long id;
        String theme;
        String title;
        String dynasty;
        String explain;
        String content;
        String author;
    }

    @Test
    public void testGet() {
        // 使用方法一，不带参数
        String url = "https://some.url/detail?id=1";
        InnerRes res = restTemplate.getForObject(url, InnerRes.class);
        System.out.println(res);


        // 使用方法一，传参替换
        url = "https://some.url/detail?id={?}";
        res = restTemplate.getForObject(url, InnerRes.class, "1");
        System.out.println(res);

        // 使用方法二，map传参
        url = "https://some.url/detail?id={id}";
        Map<String, Object> params = new HashMap<>();
        params.put("id", 1L);
        res = restTemplate.getForObject(url, InnerRes.class, params);
        System.out.println(res);

        // 使用方法三，URI访问
        URI uri = URI.create("https://some.url/detail?id=1");
        res = restTemplate.getForObject(uri, InnerRes.class);
        System.out.println(res);
    }
}
```

:question:对于`getForObject(java.lang.String, java.lang.Class<T>, java.lang.Object...)`方法，如何穿参数？
{% alert info no-icon %}

根据实际传参替换 url 模板中的内容，类似于 JDBC 中的占位符，使用模板中使用 `{?}` 来代表坑位，根据实际的传参顺序来填充

{% endalert %}

#### `getForEntity` 方式

具体方法调用原型如下：

```java
public <T> ResponseEntity<T> getForEntity(String url, Class<T> responseType, Object... uriVariables) throws RestClientException ;
public <T> ResponseEntity<T> getForEntity(String url, Class<T> responseType, Map<String, ?> uriVariables) throws RestClientException;
public <T> ResponseEntity<T> getForEntity(URI url, Class<T> responseType) throws RestClientException;
```

:sparkles:`ResponseEntity`封装中比原本直接获得 Object 多了两个东西：{% hl_text red %} 一个返回的 http 状态码{% endhl_text %}与 `ResponseHeader` 相关的头部信息

### RestTemplate – POST 请求

post 请求除了有 `forObject` 和 `forEntity` 之外，还多了个 `forLocation`；其次 post 与 get 一个明显的区别就是传参方式不同，get 的参数一般会待在 url 中；post 更常见的是**通过表单的方式提交**

```java
public <T> T postForObject(String url, @Nullable Object request, Class<T> responseType, Object... uriVariables) throws RestClientException ;

public <T> T postForObject(String url, @Nullable Object request, Class<T> responseType, Map<String, ?> uriVariables) throws RestClientException;

public <T> T postForObject(URI url, @Nullable Object request, Class<T> responseType) throws RestClientException ;
```

```java
@Test
public void testPost() {
    String url = "http://localhost:8080/post";
    String email = "xxx@gmail.com";
    String nick = "gg";

    MultiValueMap<String, String> request = new LinkedMultiValueMap<>();
    request.add("email", email);
    request.add("nick", nick);

    // 使用方法三
    URI uri = URI.create(url);
    String ans = restTemplate.postForObject(uri, request, String.class);

    // 使用方法一
    ans = restTemplate.postForObject(url, request, String.class);

    // 使用方法一，但是结合表单参数和uri参数的方式，其中uri参数的填充和get请求一致
    request.clear();
    request.add("email", email);
    ans = restTemplate.postForObject(url + "?nick={?}", request, String.class, nick);


    // 使用方法二
    Map<String, String> params = new HashMap<>();
    params.put("nick", nick);
    ans = restTemplate.postForObject(url + "?nick={nick}", request, String.class, params);
}

```

上面分别给出了三种方法的调用方式，其中 post 传参区分为两种：**uri 参数**和**表单参数**
{% alert success no-icon %}

- uri 参数和 get 请求中一样，填充 uri 中模板坑位
- 表单参数，由 `MultiValueMap` 封装，同样是 key-value 结构

{% endalert %}
{% alert warning no-icon %}

`postForLocation`是 POST 请求单独的 API，用于这样的场景：post 数据到一个 URL，返回新创建资源的 URL

{% endalert %}

```java
public URI postForLocation(String url, @Nullable Object request, Object... uriVariables)
		throws RestClientException ;

public URI postForLocation(String url, @Nullable Object request, Map<String, ?> uriVariables)
		throws RestClientException ;

public URI postForLocation(URI url, @Nullable Object request) throws RestClientException ;
```

:question:什么样的 REST 接口适合用这种 API 访问？
{% alert warning no-icon %}

一般登录 or 注册都是 post 请求，而这些操作完成之后呢？大部分都是跳转到别的页面去了，这种场景下，就可以使用 `postForLocation` 了，提交数据，并获取返回的 URI，一个测试如下

{% endalert %}

## 遇到的问题

下面主要记录使用过程中遇到的问题

### ` Could not extract response: no suitable HttpMessageConverter found for response type [class Test1] and content type [application/json;charset=UTF-8]`

出现以上问题，是由于 RestTemplate 构造的时候会缺省加载很多消息转换器，这里是由于没有增加序列化依赖，增加序列化字段相关的`setter`方法即可

## 附录

[RestTemplate 用法说明](https://www.jianshu.com/p/2a59bb937d21)
[Spring 之 RestTemplate 使用小结](https://juejin.cn/post/6844903656165212174)
[Spring RestTemplate](https://howtodoinjava.com/spring-boot2/resttemplate/spring-restful-client-resttemplate-example/)
[springMvc 官方文档-RESTClient](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#webmvc-client)
[spring 官方文档 rest-endpoint](https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#rest-client-access)
[掌握 Spring 之 RestTemplate](https://juejin.cn/post/6844903842065154061)
[rest-client-access](https://docs.spring.io/spring-framework/docs/5.1.6.RELEASE/spring-framework-reference/integration.html#rest-client-access)
