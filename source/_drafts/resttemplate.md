---
title: Spring RestTemplate
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

## 构建 `RestTemplate`
想要使用`RestTemplate`需要现在 Spring IoC 容器中创建`RestTemplate`，这里给出了几种创建方式
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
### 使用 Apache HTTPClient（推荐）
{% codeblock "基于 Apache HttpClient 的配置" lang:java %}
@Autowired
CloseableHttpClient httpClient;

@Value("${api.host.baseurl}") //配置在 application.yaml中
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
## Spring RestTemplate – HTTP GET 示例
执行 GET API 的可用方法有

|                           API 方法                           |                             含义                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|                 getForObject(url, classType)                 | 通过对 URL 执行 GET 来检索表示。响应（如果有）给定类型并返回 |
|               getForEntity(url, responseType)                |     通过对 URL 执行 GET 来检索作为*ResponseEntity*的表示     |
|    exchange(url, httpMethod, requestEntity, responseType)    |   执行指定`RequestEntity`并将响应作为*ResponseEntity*返回    |
| execute(url, httpMethod, requestCallback, responseExtractor) | 使用HTTPMethod访问给定的URI模板，设置相应的请求回调RequestCallback，并且使用ResponseExtractor检查响应信息 |

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

// pathch 
public <T> T patchForObject

// delete
public void delete()

// options
public Set<HttpMethod> optionsForAllow

// exchange
public <T> ResponseEntity<T> exchange()
```

上面提供的几个接口，基本上就是Http提供的几种访问方式的对应

### 要使用的 REST API

```java
@Autowired
UserService userService;
 
@GetMapping("users")
public ResponseEntity<List<User>> getAll() {
    return new ResponseEntity<>(userService.getAll(), HttpStatus.OK);
}
 
@GetMapping("users/{id}")
public ResponseEntity<User> getById(@PathVariable long id) {
    Optional<User> user = userService.getById(id);
    if (user.isPresent()) {
        return new ResponseEntity<>(user.get(), HttpStatus.OK);
    } else {
        throw new RecordNotFoundException();
    }
}
```

从上面的接口命名上，可以看出可以使用的有两种方式 `getForObject` 和 `getForEntity`，那么这两种有什么区别？

- 从接口的签名上，可以看出一个是直接返回预期的对象，一个则是将对象包装到 `ResponseEntity` 封装类中
- 如果只关心返回结果，那么直接用 `getForObject` 即可
- 如果除了返回的实体内容之外，还需要获取返回的header等信息，则可以使用 `getForEntity`



当我们从服务器获得**无法解析的响应**时，`getForObject()`方法非常有用，并且在固定的服务端下，只能这里访问。在这里，我们可以将响应作为`String`，并使用自定义解析器或使用字符串替换函数来修复响应，然后再将其处理给解析器

```java
private final String URI_USERS_ID = "/users/{id}";
 
@Autowired
RestTemplate restTemplate;
 
//Using RestTemplate
 
Map<String, String> params = new HashMap<String, String>();
params.put("id", "1");
 
//Parse the string after getting the response
String userStr = restTemplate.getForObject(URI_USERS_ID, String.class, params);
```





早期的 HttpURLConnection 使用非常繁琐，得写很多代码实现一个简单的请求，而 HttpClient 简单很多，OkHttp 更为简洁。
RestTemplate 进一步简化，它可以像OkHttp那样的写法，但是常见的请求基本一句代码就可以搞定，以下是常见的 GET，POST,PUT,DELETE 的用法：
```java
//1. 简单Get请求
String result = restTemplate.getForObject(rootUrl + "get1?para=my", String.class);
System.out.println("简单Get请求:" + result);

//2. 简单带路径变量参数Get请求
result = restTemplate.getForObject(rootUrl + "get2/{1}", String.class, 239);
System.out.println("简单带路径变量参数Get请求:" + result);

//3. 返回对象Get请求（注意需包含compile group: 'com.google.code.gson', name: 'gson', version: '2.8.5'）
ResponseEntity<Test1> responseEntity = restTemplate.getForEntity(rootUrl + "get3/339", Test1.class);
System.out.println("返回:" + responseEntity);
System.out.println("返回对象Get请求:" + responseEntity.getBody());

//4. 设置header的Get请求
HttpHeaders headers = new HttpHeaders();
headers.add("token", "123");
ResponseEntity<String> response = restTemplate.exchange(rootUrl + "get4", HttpMethod.GET, new HttpEntity<String>(headers), String.class);
System.out.println("设置header的Get请求:" + response.getBody());

//5. Post对象
Test1 test1 = new Test1();
test1.name = "buter";
test1.sex = 1;
result = restTemplate.postForObject(rootUrl + "post1", test1, String.class);
System.out.println("Post对象:" + result);

//6. 带header的Post数据请求
response = restTemplate.postForEntity(rootUrl + "post2", new HttpEntity<Test1>(test1, headers), String.class);
System.out.println("带header的Post数据请求:" + response.getBody());

//7. 带header的Put数据请求
//无返回值
restTemplate.put(rootUrl + "put1", new HttpEntity<Test1>(test1, headers));
//带返回值
response = restTemplate.exchange(rootUrl + "put1", HttpMethod.PUT, new HttpEntity<Test1>(test1, headers), String.class);
System.out.println("带header的Put数据请求:" + response.getBody());

//8. del请求
//无返回值
restTemplate.delete(rootUrl + "del1/{1}", 332);
//带返回值
response = restTemplate.exchange(rootUrl + "del1/332", HttpMethod.DELETE, null, String.class);
System.out.println("del数据请求:" + response.getBody());
```

## 遇到的问题

### ` Could not extract response: no suitable HttpMessageConverter found for response type [class Test1] and content type [application/json;charset=UTF-8]`
出现以上问题，是由于 RestTemplate 构造的时候会缺省加载很多消息转换器，这里是由于没有增加序列化依赖
## 附录

[RestTemplate用法说明](https://www.jianshu.com/p/2a59bb937d21)

[Spring之RestTemplate使用小结](https://juejin.cn/post/6844903656165212174)

[Spring RestTemplate](https://howtodoinjava.com/spring-boot2/resttemplate/spring-restful-client-resttemplate-example/)

https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#webmvc-client

https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#rest-client-access

