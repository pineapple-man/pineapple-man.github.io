---
title: Eureka 学习笔记
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: SpringCloud
keywords: 服务注册中心
excerpt: 本文主要记录 Spring Cloud 中的 Eureka 组件，将记录常用 API 以及使用过程中遇见的问题
date: 2021-11-22 18:00:24
thumbnailImage:
---
<!-- toc -->

>在了解 Eureka 常用操作之前，先查看一点微服务中关于服务发现的知识，这样能够帮助使用者更好的理解 Eureka 的工作模式
## 服务发现

:question:**微服务框架中，服务通常需要相互调用**，为什么需要服务发现机制？

{% alert success no-icon%}

在单体应用程序中，服务通过语言级别的方法或过程调用相互调用；在传统的分布式系统部署中，服务在固定的、众所周知的位置（主机和端口）运行，因此可以使用 HTTP/REST 或某种 RPC 机制轻松地相互调用，然而，现代基于微服务的应用程序通常在虚拟化或容器化环境中运行，其中服务的实例数量及其位置动态变化。因此，必须实现一种机制，**使服务客户端能够向一组动态变化的临时服务实例发出请求**，最终称这种机制为：服务发现
{%endalert%}

{% image fancybox fig-100 center   https://gitee.com/mingchaohu/blog-image/raw/master/image/discovery-problem.jpg %}

根据发出请求方不同，将服务发现划分为两种模式：[客户端服务发现模式](http://microservices.io/patterns/client-side-discovery.html)和[服务端服务发现模式](http://microservices.io/patterns/server-side-discovery.html)两种


###  客户端服务发现

{% alert success no-icon%}

在客户端需要向服务发出请求时，客户端**首先通过询问服务注册中心来获取服务实例的位置**（服务注册中心知道所有服务实例的位置），随后根据服务注册中心的响应进行服务的访问，下图显示了此模式的结构
{%endalert%}

{% image fancybox fig-100  center  https://gitee.com/mingchaohu/blog-image/raw/master/image/client-side-discovery.jpg %}

:sparkles:RPC远程调用框架核心设计思想在于**注册中心**，使用注册中心管理每个服务与服务之间的一个依赖关系(服务治理概念)，目前常用的注册中心架构如下图：
{% image fancybox fig-100  center  https://gitee.com/mingchaohu/blog-image/raw/master/image/3956561052b9dc3909f16f1ff26d01bb.png %}

:question:什么是服务注册中心？
{% alert success no-icon%}

服务注册中心就是**服务的登记处**，类似于生活中的社区登记处，用于记录整个系统中所有服务的位置，服务注册中心的位置时固定不变的，客户端通过访问服务注册中心查询到动态变化服务的位置。当服务漂移之后，服务会主动将目前所在位置通报给服务注册中心，服务注册中心随后在内部更改服务的位置，确保下次客户端能够正确获得服务漂移之后的位置
{%endalert%}

服务发现依赖于服务注册表，系统中每个服务实例启动时，会将自己的网络位置信息发送到服务注册表，服务注册表利用心跳机制即时更新。实例关闭或者服务注册表检测到实例心跳超时情况下，实例信息就会从服务注册表移出
### 服务器端服务发现

{% alert success no-icon%}

当向服务发出请求时，客户端通过运行在众所周知的位置的负载均衡器（`Router`）发出请求，路由器查询可能内置于负载均衡器中的服务注册表，并将请求转发给可用的服务实例，下图显示了此模式的结构
{%endalert%}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/server-side-discovery.jpg %}


:vs:服务器端服务与客户端服务发现的异同
{% alert success no-icon%}
- 与客户端发现相比，客户端代码更加简单，因为它不必处理发现，相反客户端只是向负载均衡器发出请求，随后还需要根据负载均衡器返回的结果，在客户端再一次请求真正的服务

- 除非服务端服务发现是云环境的一部分，否则路由器必须是另一个必须安装和配置的系统组件。它还需要复制以提高可用性和容量，路由器必须支持必要的通信协议（例如 HTTP、gRPC、Thrift 等），需要比使用客户端发现进行更多的网络跳数；客户端服务发现就没有这种限制
{%endalert%}

### 总结

{% alert info no-icon%}

对于两种不同类似的服务发现，主要区别在于：**服务发现和负载均衡策略是由使用方自己实现还是作为一项服务来供使用方调用**

客户端发现模式是由服务请求方负责发现所有可用实例在网络中的具体位置，并根据具体的Balance策略将请求路由到具体的实例处理。而服务端发现模式则是请求方把请求经由Load Balancer，Load Balancer 查询服务注册表后根据自己的 Balance 策略将请求路由到目标服务的一台具体实例上进行处理
{%endalert%}

## Eureka 概述
{% alert success no-icon%}

Eureka 是一种客户端服务发现模式，提供 Server 和 Client 两个组件。Eureka Server 作为服务注册表的角色，提供 REST API 管理服务实例的注册和查询。`POST`请求用于服务注册，`PUT`请求用于实现心跳机制，`DELETE`请求服务注册表移除实例信息，`GET`请求查询服务注册表来获取所有的可用实例。Eureka Client 是 Eureka 客户端，除了方便集成外，还提供了比较简单的`Round-Robin Balance`，配合使用 `Netflix Ribbon`，可以实现更加复杂的基于流量、资源占用情况、请求失败等因素的 Banlance 策略，为系统提供更可靠的弹性保证
{%endalert%}

Eureka 最初在 Netflix 主要用于 AWS 云上的服务发现，以实现服务器的负载均衡和容错。Eureka 也适用于基于 Docker 等虚拟化计数的微服务架构环境中，Spring Cloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务治理。

### Eureka 的服务注册与发现

之前阐述过，进行服务注册与发现时，存在一个**注册中心**，当服务器启动的时候，各个服务提供者会把将当前服务器的信息（比如**服务地址通讯地址**等以别名方式）**注册到注册中心上**。**服务消费者使用该别名的方式去注册中心上获取到实际的服务通讯地址**，获取到通讯地址后，就可以进行RPC调用

{% alert success no-icon%}

:sparkles:Eureka是一个AP的系统，具备高可用性和分区容错性。每个 Eureka Client 本地都有一份它最新获取到的服务注册表的缓存信息，即使所有的Eureka Server都挂掉了，依然可以根据本地缓存的服务信息正常工作

{%endalert%}

Eureka Server没有基于 `quorum` 机制实现，而是采用 `p2p` 的去中心化结构，这样相比于`zookeeper`，集群不需要保证至少 $((n+1)/2)$台 Server 存活才能正常工作，增强了可用性；但是这种结构注定了 Eureka 不可能有 zookeeper 那样的一致性保证，因为 Client 缓存更新不及时或Server 间同步失败等原因，都会导致 Client 访问不到新注册的服务或者访问到过期的服务

{% blockquote  %}

**Quorum** 机制，是一种分布式系统中常用的，用来保证数据冗余和最终一致性的投票算法，其主要数学思想来源于鹊巢原理，由于不是本文重点，所以不在这里进行阐述

{% endblockquote%}

当 Eureka Server 节点间某次信息同步失败时，同步失败的操作会在客户端下次心跳发起时再次同步；如果 Eureka Server 和 Eureka Client 间有网络分区存在，Eureka Server会进入**自我保护模式**，不再把过期服务从服务注册表移除(这种情况下客户端有可能获取已经停止的服务，配合使用`Hystrix`通过熔断机制来容错和降级，弥补基于客户端服务发现的时效性缺点)

### Eureka 组件

:vs:Eureka 包含两个组件:**Eureka Server** 和 **Eureka Client**，下面主要介绍两者的区别
{% alert success no-icon%}

**Eureka Server**提供服务注册服务，各个微服务节点通过配置启动后，会在 EurekaServer 中进行注册，这样 EurekaServer 中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观看到

**Eureka Client**通过注册中心进行访问，它是一个 Java 客户端，用于简化 Eureka Server 的交互，客户端同时也具备一个内置的、使用轮询(round-robin)负载算法的负载均衡器。在应用启动后，客户端将会向 Eureka Server 发送心跳(默认周期为30秒)。如果 Eureka Server 在多个心跳周期内没有接收到某个节点的心跳，EurekaServer 将会从服务注册表中把这个服务节点移除（默认90秒)
{%endalert%}


## 使用

Eureka 可以和 Spring Cloud 无缝集成，本小节开始阐述 Eureka 常用操作。

### 服务端（Eureka Server）
{% alert warning no-icon%}

添加组件依赖
{%endalert%}
```xml
<!-- eureka-server -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
<!-- 其他依赖，主要是Spring Cloud及Spring Cloud Alibaba的依赖 -->
```
{% alert warning no-icon%}

组件配置（Eureka 的配置）
{%endalert%}
```yaml
# Eureka在Spring boot 项目中的配置文件
server:
  port: 6868
eureka:
  instance:
    hostname: localhost
  client:
    #false表示不向注册中心注册自己。
    register-with-eureka: false
    #false表示自己就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      # 设置与Eureka server交互的地址查询服务和注册服务都需要依赖这个地址。
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```
{% alert success no-icon%}

`defaultZone`是一个魔数字符串默认值，作用是给没有指定`Zone`的客户端一个默认的`Eureka`地址，客户端可以在配置文件中指定当前服务属于哪一个`Zone`，如果没有指定，则属于默认`Zone`
{%endalert%}

默认的应用名(Service ID)和端口号分别对应配置信息中的`${spring.application.name}`和`${server.port}`参数
{% alert warning no-icon%}
`Spring Boot`启动类
{%endalert%}
```java
@SpringBootApplication()
@EnableEurekaServer
public class ServiceRegistry {
   public static void main(String[] args) {
      SpringApplication.run(ServiceRegistry.class, args);
   }
}
```

随后就可以启动服务，访问`http://localhost:6868`就可以查看`Eureka`服务页面，由于只有服务端没有客户端所以看不到任何注册的服务，接下来介绍如何增加客户端

### 客户端
{% alert warning no-icon%}

添加组件依赖，这里增加的是客户端 Eureka 依赖而不是服务端
{%endalert%}
```xml
<!--eureka client-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```
{% alert success no-icon%}
组件配置（Eureka 客户端的配置）
{%endalert%}
```yaml
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:6868/eureka
```
{% alert warning no-icon%}
`Spring Boot`启动类
{%endalert%}
```java
@SpringBootApplication()
@EnableEurekaClient	//客户端配置
public class CompanyApplication {
   public static void main(String[] args) {
      SpringApplication.run(CompanyApplication.class, args);
   }
   
}
```
使用`@EnableEurekaClient`注解后，当前应用会同时变成 Eureka 服务端(它会注册自身)和 Eureka 客户端(可以查询当前服务列表)，与此相关的配置都在以`eureka.instance.*`开头的参数下，下图是启动 Eureka 后，访问当前系统内已注册的服务（由于存在网络分区所以默认开启的自我保护模式）

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20211109170317330.png)

画面中的那一段英文，表示的就是当前 Eureka 开启了自我保护模式，具体的自我保护机制在后面会有一小节内容进行介绍
>EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE.
{% alert info no-icon%}

:notebook:不要在`@PostConstruct`或`@Scheduled`方法中使用`EurekaClient`。在`ApplicationContext`还没有完全启动时使用该对象会发生错误
{%endalert%}
## Eureka 集群

{% alert warning no-icon%}

微服务 RPC 远程服务调用最核心的就是高可用性，试想你的注册中心只有一个，万一它出故障了，会导致整个为服务环境不可用，所以需要搭建 Eureka 注册中心集群，实现**负载均衡+故障容错**。
{%endalert%}

完成 Eureka 集群的实现非常简单，就是通过将两个 Eureka 服务做到：**互相注册，互相守望**即可做到 Eureka 集群的高可用

{% image fancybox fig-100  center  https://gitee.com/mingchaohu/blog-image/raw/master/image/14570c4b7c4dd8653be6211da2675e45.png %}

:sailboat:使用步骤
{% alert success no-icon%}

1. 先启动 Eureka 注主册中心
2. 启动**服务提供者** payment 支付服务
3. 服务启动后会把自身信息(服务地址以别名方式注朋进 eureka)
4. 消费者 order 服务在需要调用接口时，**使用服务别名去注册中心获取服务的远程地址**
5. 消费者获得调用地址后，底层实际是利用 HttpClient 技术实现远程调用
6. 消费者会将服务地址后会缓存在本地 JVM 内存中，默认每间隔30秒更新一次服务调用地址

{%endalert%}

默认已经将`eureka7002.com` 和 `eureka7001.com` 映射到了本地`host`文件，映射地址就是`127.0.0.1`
{% codeblock "Eureka 集群 1 配置" lang:yaml %}
# 7001 微服务配置
server:
  port: 7001

eureka:
  instance:
    hostname: eureka7001.com #eureka服务端的实例名称
  client:
    register-with-eureka: false     #false表示不向注册中心注册自己。
    fetch-registry: false     #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:
    #集群指向其它eureka
      defaultZone: http://eureka7002.com:7002/eureka/
    #单机就是7001自己
      #defaultZone: http://eureka7001.com:7001/eureka/
{% endcodeblock %}


{% codeblock "Eureka 集群 2 配置" lang:yaml %}
# 7002 微服务配置
server:
  port: 7002

eureka:
  instance:
    hostname: eureka7002.com #eureka服务端的实例名称
  client:
    register-with-eureka: false     #false表示不向注册中心注册自己。
    fetch-registry: false     #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:
    #集群指向其它eureka
      defaultZone: http://eureka7001.com:7001/eureka/
    #单机就是7002自己
      #defaultZone: http://eureka7002.com:7002/eureka/

{% endcodeblock %}

此时由于拥有了两个微服务注册中心，所以每一个客户端都需要更改注册中心的位置信息
{% codeblock "支付微服务需要增加的配置" lang:yaml %}
eureka:
  client:
    #表示是否将自己注册进Eurekaserver默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka, http://eureka7002.com:7002/eureka
{% endcodeblock %}

1. 先要启动EurekaServer，7001/7002服务
2. 再要启动服务提供者provider，8001
3. 再要启动消费者

在使用过程中，遇到如下问题：
{% alert success no-icon%}
-  HTTP 可以启动，但是使用 HTTPS就会出错（服务注册失败）不知道为什么？
{%endalert%}
## 服务发现（Discovery）
对于注册进 eureka 里面的微服务，可以通过**服务发现**来获得该服务的信息


{% codeblock "Eureka 客户端启动类" lang:java %}
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient//添加该注解
public class PaymentMain001 {

    public static void main(String[] args) {
        SpringApplication.run(PaymentMain001.class, args);
    }
}
{% endcodeblock %}

{% codeblock "Eureka 客户端 Controller文件" lang:java %}
@RestController
public class PaymentController{
	...
    
    @Resource
    private DiscoveryClient discoveryClient;

    ...

    @GetMapping(value = "/payment/discovery")
    public Object discovery()
    {
        List<String> services = discoveryClient.getServices();
        for (String element : services) {
            log.info("*****element: "+element);
        }
        //获取服务别名为 CLOUD-PAYMENT-SERVICE 的服务地址
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info(instance.getServiceId()+"\t"+instance.getHost()+"\t"+instance.getPort()+"\t"+instance.getUri());
        }
        return this.discoveryClient;
    }
}
{% endcodeblock %}

## Eureka自我保护

自我保护模式通常在一组客户端和 Eureka Server 之间存在网络分区场景下使用，一旦进入保护模式，Eureka Server 将会尝试保护其服务注册表中的信息，不再删除服务注册表中的数据，也就是**不会注销任何微服务**。默认`Eureka`开启了自我保护模式，进入首页的红字就是提示开启了保护模式

:question:什么是自我保护模式
{% alert success no-icon%}

默认情况下，如果 Eureka Server 在一定时间内（默认时间为：90 秒）没有接收到某个微服务实例的心跳，Eureka Server 将会注销该实例。但是当网络分区故障发生时(例如：延时、卡顿、拥挤)，微服务客户端与 Eureka Server 之间无法正常通信，以上行为可能变得非常危险。因为微服务本身其实是健康的，此时本不应该注销这个微服务，错误的删除可能导致服务的丢失。Eureka 通过**自我保护模式**来解决这个问题，做到 AP 分布式系统。
{%endalert%}

当 Eureka Server 节点在短时间内丢失过多客户端时(可能发生了网络分区故障)，那么这个节点就会进入自我保护模式，**在自我保护模式中，Eureka Server会保护服务注册表中的信息，不再注销任何服务实例**，自我保护默认是开启的，也可以通过使用`eureka.server.enable-self-preservation = false`禁用自我保护模式关闭

{% alert success no-icon%}

自我保护模式的设计哲学就是：宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例
{%endalert%}

{% alert info no-icon%}

:notebook:综上，自我保护模式是一种应对网络异常的安全保护措施。它的架构哲学是**宁可同时保留所有微服务（健康的微服务和不健康的微服务都会保留）也不盲目注销任何健康的微服务**；使用自我保护模式，可以让Eureka集群更加的健壮、稳定
{%endalert%}
{% codeblock "Eureka Client 心跳检测与持续时间配置" lang:yaml %}
eureka:
  ...
  instance:
    instance-id: payment8001
    prefer-ip-address: true
    #
    #开发时没置小些，保证服务关闭后注册中心能即使剔除服务
    #Eureka客户端向服务端发送心跳的时间间隔，单位为秒(默认是30秒)
    lease-renewal-interval-in-seconds: 1
    #Eureka服务端在收到最后一次心跳后等待时间上限，单位为秒(默认是90秒)，超时将剔除服务
    lease-expiration-duration-in-seconds: 2
{% endcodeblock %}
{% alert info no-icon%}

:notes:最好不要修改 30 秒的默认心跳间隔，因为 Server 会使用这个时间数值来判断是否出现了大面积故障
{%endalert%}
## 其他
接下来记录 Eureka 其他功能

### Eureka Server的身份验证

如果客户端的`eureka.client.serviceUrl.defaultZone`参数值(即Eureka Server的地址)中包含`HTTP Basic Authentication`信息，如`http://user:password@localhost:8761/eureka`，那么客户端就会自动使用该用户名、密码信息与Eureka服务端进行验证。如果需要更复杂的验证逻辑，必须注册一个`DiscoveryClientOptionalArgs`组件，并将`ClientFilter`组件注入，在这里定义的逻辑会在每次客户端向服务端发起请求时执行。由于Eureka的限制，Eureka不支持单节点身份验证

### 状态页和健康信息指示器

Eureka应用的状态页和健康信息默认的 url 为：`/info`和`/health`，这与`Spring Boot Actuator`中对应的 Endpoint 是重复的，因此必须进行修改，修改后的配置文件如下：

{% codeblock "Eureka 服务端状态页和健康信息器配置文件" lang:yaml %}
eureka:
  instance:
    statusPageUrlPath: ${management.context-path}/info
    healthCheckUrlPath: ${management.context-path}/health
{% endcodeblock %}

### 使用HTTPS

可以通过指定`EurekaInstanceConfig`类中的`eureka.instance.[nonSecurePortEnabled,securePortEnabled]=[false,true]`属性来指定是否使用 HTTPS。当配置使用 HTTPS 时，Eureka Server 会返回以`https`开头的服务地址。即使配置了使用 HTTPS，Eureka 的主页依然是以普通 HTTP 方式访问的，需要手动添加一些配置来将这些页面也通过 HTTPS 保护起来
{% codeblock "Eureka Service 配置文件" lang:yaml %}
eureka:
  instance:
    statusPageUrl: https://${eureka.hostname}/info
    healthCheckUrl: https://${eureka.hostname}/health
    homePageUrl: https://${eureka.hostname}/
{% endcodeblock %}

:notes:`eureka.hostname`是 Eureka 原生属性，只有新版本的 Eureka 才支持该属性，也可以使用Spring EL表达式代替`${eureka.instance.hostName}`

### 健康检查

默认情况下，Eureka通过客户端发来的心跳包来判断客户端是否在线。如果不显式指定，客户端在心跳包中不会包含当前应用的健康数据。这意味着只要客户端启动时完成了服务注册，那么该客户端在主动注销之前在 Eureka 中的状态会永远是`UP`状态。我们可以通过配置修改这一默认行为，即在客户端发送心跳包时会带上自己的健康信息。这样做的后果是只有当该服务的状态是`UP`时才能被访问，其它的任何状态都会导致该服务不能被调用
{% codeblock "Eureka Service 配置文件" lang:yaml %}
eureka:
  client:
    healthcheck:
      enabled: true
{% endcodeblock %}

如果想对健康检查有更细粒度的控制，可以自己实现`com.netflix.appinfo.HealthCheckHandler`接口，做到自定义健康检查机制

### Eureka 元数据说明

了解 Eureka 的元数据，就可以添加一些自定义的数据以适应特定的业务场景。像主机名、IP地址、端口号、状态页 url 和健康检查 url 都是 Eureka 定义的标准元数据。这些元数据会被保存在 Eureka Server的注册信息中，客户端会读取这些数据来向需要调用的服务直接发起连接。可以使用以`eureka.instance.metadataMap`开头的参数来添加自定义的元数据，所有客户端都会读取到该信息。通过这种方式你能给客户端自定义一些行为

### 使用 Spring 的 DiscoveryClient 对象

没有必要直接使用 Netflix 原生的`EurekaClient`对象，在此基础上做一些封装使用起来会更方便。Spring Cloud支持`Feign`和`Spring RestTmpelate`，它们都可以使用服务的逻辑名而不是 URL 地址来查询服务。也可以使用 Spring 提供的`DiscoveryClient`对象访问远程服务，从而代码就不会与 Eureka 紧耦合

```java
@Autowired
private DiscoveryClient discoveryClient;

public String serviceUrl() {
    List<ServiceInstance> list = discoveryClient.getInstances("STORES");
    if (list != null && list.size() > 0 ) {
        return list.get(0).getUri();
    }
    return null;
}
```
### 伙伴感知

Eureka Server 可以通过运行多个实例并相互指定为**伙伴**的方式来达到更高的高可用性。实际上这就是默认设置，只需要指定伙伴的地址就可以了，这也就是之前说的 Eureka 集群方式

{% codeblock "Eureka Server伙伴感知配置文件" lang:yaml %}
---
spring:
  profiles: peer1
eureka:
  instance:
    hostname: peer1
  client:
    serviceUrl:
      defaultZone: http://peer2/eureka/

---
spring:
  profiles: peer2
eureka:
  instance:
    hostname: peer2
  client:
    serviceUrl:
      defaultZone: http://peer1/eureka/
{% endcodeblock %}

在上面这个例子中，通过使用不同`profile`配置的方式可以在本地运行两个Eureka Server。可以通过修改`/etc/host`文件，使用上述配置在本地测试伙伴感特性。可以同时启动多个Eureka Server, 并通过伙伴配置使之围成一圈(相邻两个Server互为伙伴)，这些Server中的注册信息都是同步的。如果伙伴在物理上是分开的，那么系统原则上可以承受脑裂类型的故障

### Eureka 的高可用

Eureka把所有注册信息都放在**内存**中，所有注册过的客户端都会向 Eureka 发送心跳包来保持连接。客户端会有一份本地注册信息的缓存，这样就不需要每次远程调用时都向 Eureka 查询注册信息。默认情况下，Eureka 服务端自身也是个客户端，所以需要指定一个 Eureka Server 的 URL 作为"伙伴"(peer)。如果没有提供这个地址，Eureka Server 也能正常启动工作，但是在日志中会有大量关于找不到 peer 的错误信息

### 为什么注册一个服务这么慢?

服务的注册涉及到心跳连接，默认为每30秒一次。只有当 Eureka 服务端和客户端本地缓存中的服务元数据相同时这个服务才能被其它客户端发现，这需要 3 个心跳周期。可以通过参数`eureka.instance.leaseRenewalIntervalInSeconds`调整这个时间间隔来加快这个过程。在生产环境中最好使用默认值，因为Eureka内部的某些计算依赖于该时间间隔

### Standalone 模式

只要 Eureka Server 进程不会挂掉，这种集 Server 和 Client 于一身的模式能让 Standalone 部署的 Eureka Server 非常容易进行灾难恢复。在 Standalone 模式中，可以通过下面的配置来关闭查找伙伴的行为
{% codeblock "Eureka Service Standlone 配置文件" lang:yaml %}
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
{% endcodeblock %}

:notes:`serviceUrl`中的地址的主机名要与本地主机名相同

### 使用IP地址

有些时候可能更倾向于直接使用IP地址定义服务而不是使用主机名。把`eureka.instance.preferIpAddress`参数设为`true`时，客户端在注册时就会使用自己的 ip 地址而不是主机名

## 附录

[官方中文文档](http://docs.springcloud.cn/user-guide/eureka/)

[微服务之服务发现 Eureka的介绍与使用](https://martian101.github.io/2017/04/20/%E5%BE%AE%E6%9C%8D%E5%8A%A1%E4%B9%8B%E6%9C%8D%E5%8A%A1%E5%8F%91%E7%8E%B0-Eureka%E7%9A%84%E4%BB%8B%E7%BB%8D%E4%B8%8E%E4%BD%BF%E7%94%A8/)

[Eureka学习文档资料](http://docs.springcloud.cn/user-guide/eureka/)
