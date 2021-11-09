---
title: Eureka 学习笔记
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: Spring 全家桶
tags: 服务注册中心
keywords: 服务注册中心
excerpt: Eureka
---
## 概述

Spring Cloud封装了Netflix 公司开发的Eureka模块来实现服务治理，在Netflix主要用于AWS云上的服务发现，以实现中间层服务器的负载均衡和容错。Eureka也适用于基于Docker等虚拟化计数的微服务架构环境中

## 服务发现

服务通常需要相互调用。在单体应用程序中，服务通过语言级别的方法或过程调用相互调用。在传统的分布式系统部署中，服务在固定的、众所周知的位置（主机和端口）运行，因此可以使用 HTTP/REST 或某种 RPC 机制轻松地相互调用。然而，现代基于微服务的应用程序通常在虚拟化或容器化环境中运行，其中服务的实例数量及其位置动态变化。因此，必须实现一种机制，使服务客户端能够向一组动态变化的临时服务实例发出请求。根据发出请求方不同，将服务发现划分为两种模式：[客户端服务发现模式](http://microservices.io/patterns/client-side-discovery.html)和[服务端服务发现模式](http://microservices.io/patterns/server-side-discovery.html)两种

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/discovery-problem.jpg)

###  客户端服务发现

- 服务的每个实例都在特定位置（主机和端口）公开远程 API，例如 HTTP/REST 或 Thrift 等
- 服务实例的数量及其位置动态变化
- 虚拟机和容器通常分配有动态 IP 地址
- 服务实例的数量可能会动态变化。例如，EC2 Autoscaling Group 根据负载调整实例数

解决方案

当向服务发出请求时，客户端通过查询服务注册中心来获取服务实例的位置（服务注册中心知道所有服务实例的位置），下图显示了此模式的结构

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/client-side-discovery.jpg)

### 服务器端服务发现

当向服务发出请求时，客户端通过运行在众所周知的位置的路由器（负载均衡器）发出请求，路由器查询可能内置于路由器中的服务注册表，并将请求转发给可用的服务实例，下图显示了此模式的结构

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/server-side-discovery.jpg)

服务器端服务发现有很多好处

- 与客户端发现相比，客户端代码更加简单，因为它不必处理发现，相反客户端只是向负载均衡器发出请求
- 一些云环境提供此功能，例如 AWS Elastic Load Balancer

它也有以下缺点：

- 除非它是云环境的一部分，否则路由器必须是另一个必须安装和配置的系统组件。它还需要复制以提高可用性和容量。
- 除非是基于 TCP 的路由器，否则路由器必须支持必要的通信协议（例如 HTTP、gRPC、Thrift 等）
- 需要比使用客户端发现更多的网络跳数


### 总结

区别在于服务发现和负载均衡策略是由使用方自己实现还是作为一项服务来供使用方调用。服务发现依赖于服务注册表，系统中每个服务实例启动时，会将自己的网络位置信息发送到服务注册表，服务注册表利用心跳机制即时更新。实例关闭或者服务注册表检测到实例心跳超时情况下，实例信息就会从服务注册表移出。

具体到这两种不同的服务发现模式，客户端发现模式是由服务请求方负责发现所有可用实例在网络中的具体位置，并根据具体的Balance策略将请求路由到具体的实例处理。而服务端发现模式则是请求方把请求经由Load Balancer，Load Balancer查询服务注册表后根据自己的Balance策略将请求路由到目标服务的一台具体实例上进行处理。

Eureka 是一种客户端服务发现模式，提供Server和Client两个组件。Eureka Server作为服务注册表的角色，提供REST API管理服务实例的注册和查询。`POST`请求用于服务注册，`PUT`请求用于实现心跳机制，`DELETE`请求服务注册表移除实例信息，`GET`请求查询服务注册表来获取所有的可用实例。Eureka Client是Java实现的Eureka客户端，除了方便集成外，还提供了比较简单的Round-Robin Balance。配合使用`Netflix Ribbon`，可以实现更加复杂的基于流量、资源占用情况、请求失败等因素的Banlance策略，位系统提供更可靠的弹性保证

## 服务治理

在传统的 RPC 远程调用框架中，每个服务于服务之间依赖关系十分复杂，管理起来比较繁琐，所以需要使用一种智能的服务治理（管理）软件，来管理服务于服务之间依赖关系，实现服务调用、服务发现、服务注册、负载和容错等

## 服务注册与发现

Eureka采用了 CS 的设计架构，Eureka Server 作为服务注册功能的服务器，它是服务注册中心，而系统中的其他微服务，使用Eureka的客户端连接到 Eureka Server 并维持心跳连接。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行

在服务注册与发现中，存在一个**注册中心**。当服务器启动的时候，各个服务提供者会把当前自己服务器的信息（比如**服务地址通讯地址**等以别名方式）**注册到注册中心上**。服务消费者使用该别名的方式去注册中心上获取到实际的服务通讯地址，然后再进行RPC调用

服务注册在Eureka Server上，每30秒发送心跳来维持注册状态。客户端90秒内都没有发心跳，Eureka Server就会认为服务不可用并将其从服务注册表移除。服务的注册和更新信息会同步到Eureka集群的其他节点。所有zone的Eureka Client每30秒查询一次当前zone的服务注册表获取所有可用服务，然后采用合适的Balance策略向某一个服务实例发起请求

Eureka是一个AP的系统，具备高可用性和分区容错性。每个Eureka Client本地都有一份它最新获取到的服务注册表的缓存信息，即使所有的Eureka Server都挂掉了，依然可以根据本地缓存的服务信息正常工作。

Eureka Server没有基于`quorum`机制实现，而是采用`p2p`的去中心化结构，这样相比于zookeeper，集群不需要保证至少几台Server存活才能正常工作，增强了可用性。但是这种结构注定了Eureka不可能有zookeeper那样的一致性保证，同时因为Client缓存更新不及时、Server间同步失败等原因，都会导致Client访问不到新注册的服务或者访问到过期的服务。

当Eureka Server节点间某次信息同步失败时，同步失败的操作会在客户端下次心跳发起时再次同步；如果Eureka Server和Eureka Client间有网络分区存在(默认的检测机制是15分钟内低于85%的心跳汇报)，Eureka Server会进入自我保护模式，不再把过期服务从服务注册表移除(这种情况下客户端有可能获取已经停止的服务，配合使用`Hystrix`通过熔断机制来容错和降级，弥补基于客户端服务发现的时效性的缺点)

> **Quorum** 机制，是一种分布式系统中常用的，用来保证数据冗余和最终一致性的投票算法，其主要数学思想来源于鹊巢原理



:sparkles:RPC远程调用框架核心设计思想：**注册中心**，因为使用注册中心管理每个服务与服务之间的一个依赖关系(服务治理概念)

:notes:在任何RPC远程框架中，都会有一个注册中心存放服务地址相关信息(接口地址)



![](https://gitee.com/mingchaohu/blog-image/raw/master/image/3956561052b9dc3909f16f1ff26d01bb.png)

**Eureka包含两个组件:Eureka Server和Eureka Client**

**Eureka Server**提供服务注册服务

各个微服务节点通过配置启动后，会在EurekaServer中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观看到

**EurekaClient**通过注册中心进行访问

它是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、使用轮询(round-robin)负载算法的负载均衡器。

在应用启动后，将会向Eureka Server发送心跳(默认周期为30秒)。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务注册表中把这个服务节点移除（默认90秒)

## 使用

Eureka可以和Spring Cloud无缝集成，这里主要说明一下和Spring Cloud的集成

### 服务端

```xml
<!-- eureka-server -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
<!-- 其他依赖，主要是Spring Cloud及Spring Cloud Alibaba的依赖 -->
```

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

`defaultZone`是一个魔数字符串默认值，作用时给没有指定`Zone`的客户端一个默认的`Eureka`地址

客户端可以在配置文件中指定当前服务属于哪一个`Zone`，如果没有指定，则属于默认`Zone`

默认的应用名(Service ID)、主机名和端口号分别对应配置信息中的`${spring.application.name}`、`${spring.application.name}`和`${server.port}`参数

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

```xml
<!--eureka client-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

```java
@SpringBootApplication()
@EnableEurekaClient	//客户端配置
public class CompanyApplication {
   public static void main(String[] args) {
      SpringApplication.run(CompanyApplication.class, args);
   }
   
}
```

```yaml
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:6868/eureka
```

使用`@EnableEurekaClient`注解后当前应用会同时变成一个Eureka服务端实例(它会注册自身)和Eureka客户端(可以查询当前服务列表)，与此相关的配置都在以`eureka.instance.*`开头的参数下。只要你指定了`spring.application.name`参数，那么就可以放心的使用默认参数而不需要修改任何配置

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20211109170317330.png)

EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE.

紧急情况！EUREKA可能错误地声称实例在没有启动的情况下启动了。续订小于阈值，因此实例不会为了安全而过期。

## Eureka集群原理说明

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/14570c4b7c4dd8653be6211da2675e45.png)

服务注册：将服务信息注册进注册中心

服务发现：从注册中心上获取服务信息

实质：存key服务命取value服务提供的地址

:sailboat:使用步骤

1. 先启动eureka注主册中心
2. 启动服务提供者payment支付服务
3. 服务启动后会把自身信息(比服务地址以别名方式注朋进eureka
4. 消费者order服务在需要调用接口时，使用服务别名去注册中心获取实际的RPC远程调用地址
5. 消费者导调用地址后，底屋实际是利用HttpClient技术实现远程调用
6. 消费者会将服务地址后会缓存在本地jvm内存中，默认每间隔30秒更新—次服务调用地址

微服务RPC远程服务调用最核心的是什么

高可用，试想你的注册中心只有一个only one，万一它出故障了，会导致整个为服务环境不可用，所以需要搭建Eureka注册中心集群，实现负载均衡+故障容错。**互相注册，互相守望**，做到两个`Eureka`服务相互注册

```yaml
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
```

```yaml
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
```

此时由于拥有了两个微服务注册中心，所以每一个客户端都需要更改注册中心的位置信息

```yaml
eureka:
  client:
    #表示是否将自己注册进Eurekaserver默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka, http://eureka7002.com:7002/eureka
```

1. 先要启动EurekaServer，7001/7002服务
2. 再要启动服务提供者provider，8001
3. 再要启动消费者

## 服务发现（Discovery）实现

对于注册进eureka里面的微服务，可以通过服务发现来获得该服务的信息

```java
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient//添加该注解
public class PaymentMain001 {

    public static void main(String[] args) {
        SpringApplication.run(PaymentMain001.class, args);
    }
}

```

```java
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

        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info(instance.getServiceId()+"\t"+instance.getHost()+"\t"+instance.getPort()+"\t"+instance.getUri());
        }

        return this.discoveryClient;
    }
}

```

## Eureka自我保护

保护模式主要用于一组客户端和Eureka Server之间存在网络分区场景下的保护。一旦进入保护模式，Eureka Server将会尝试保护其服务注册表中的信息，不再删除服务注册表中的数据，也就是不会注销任何微服务。默认`Eureka`开启了自我保护模式，进入首页的红字就是提示开启了保护模式

:thinking:为什么会产生`Eureka`的自我保护机制？

为了EurekaClient可以正常运行，防止与EurekaServer网络不通情况下，EurekaServer不会立刻将EurekaClient服务剔除

:question:什么是自我保护模式

默认情况下，如果EurekaServer在一定时间内没有接收到某个微服务实例的心跳，EurekaServer将会注销该实例(默认90秒)。但是当网络分区故障发生(延时、卡顿、拥挤)时，微服务与EurekaServer之间无法正常通信，以上行为可能变得非常危险了——因为微服务本身其实是健康的，此时本不应该注销这个微服务。Eureka通过**自我保护模式**来解决这个问题——当EurekaServer节点在短时间内丢失过多客户端时(可能发生了网络分区故障)，那么这个节点就会进入自我保护模式



自我保护机制∶默认情况下EurekaClient定时向EurekaServer端发送心跳包

如果Eureka在server端在一定时间内(默认90秒)没有收到EurekaClient发送心跳包，便会直接从服务注册列表中剔除该服务，但是在短时间( 90秒中)内丢失了大量的服务实例心跳，这时候Eurekaserver会开启自我保护机制，不会剔除该服务（该现象可能出现在如果网络不通但是EurekaClient为出现宕机，此时如果换做别的注册中心如果一定时间内没有收到心跳会将剔除该服务，这样就出现了严重失误，因为客户端还能正常发送心跳，只是网络延迟问题，而保护机制是为了解决此问题而产生的)
**在自我保护模式中，Eureka Server会保护服务注册表中的信息，不再注销任何服务实例**

它的设计哲学就是宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例。一句话讲解：**好死不如赖活着**

综上，自我保护模式是一种应对网络异常的安全保护措施。它的架构哲学是宁可同时保留所有微服务（健康的微服务和不健康的微服务都会保留）也不盲目注销任何健康的微服务。使用自我保护模式，可以让Eureka集群更加的健壮、稳定

使用`eureka.server.enable-self-preservation = false`可以禁用自我保护模式

**THE SELF PRESERVATION MODE IS TURNED OFF. THIS MAY NOT PROTECT INSTANCE EXPIRY IN CASE OF NETWORK/OTHER PROBLEMS**

默认：

```properties
eureka.instance.lease-renewal-interval-in-seconds=30
eureka.instance.lease-expiration-duration-in-seconds=90
```

```yaml
eureka:
  ...
  instance:
    instance-id: payment8001
    prefer-ip-address: true
    #心跳检测与续约时间
    #开发时没置小些，保证服务关闭后注册中心能即使剔除服务
    #Eureka客户端向服务端发送心跳的时间间隔，单位为秒(默认是30秒)
    lease-renewal-interval-in-seconds: 1
    #Eureka服务端在收到最后一次心跳后等待时间上限，单位为秒(默认是90秒)，超时将剔除服务
    lease-expiration-duration-in-seconds: 2
```

## Eureka Server的身份验证

如果客户端的`eureka.client.serviceUrl.defaultZone`参数值(即Eureka Server的地址)中包含`HTTP Basic Authentication`信息，如`[http://user:password@localhost:8761/eureka](http://user:password@localhost:8761/eureka)`，那么客户端就会自动使用该用户名、密码信息与Eureka服务端进行验证。如果你需要更复杂的验证逻辑，你必须注册一个`DiscoveryClientOptionalArgs`组件，并将`ClientFilter`组件注入，在这里定义的逻辑会在每次客户端向服务端发起请求时执行。

由于Eureka的限制，Eureka不支持单节点身份验证

## 状态页和健康信息指示器

Eureka应用的状态页和健康信息默认的url为`/info`和`/health`，这与`Spring Boot Actuator`中对应的Endpoint是重复的，因此你必须进行修改

```yaml
eureka:
  instance:
    statusPageUrlPath: ${management.context-path}/info
    healthCheckUrlPath: ${management.context-path}/health
```

客户端通过这些URL获取数据，并根据这些数据来判断是否可以向某个服务发起请求

## 使用HTTPS

你可以指定`EurekaInstanceConfig`类中的`eureka.instance.[nonSecurePortEnabled,securePortEnabled]=[false,true]`属性来指定是否使用HTTPS。当配置使用HTTPS时，Eureka Server会返回以`https`开头的服务地址

即使配置了使用HTTPS，Eureka的主页依然是以普通 HTTP 方式访问的。你需要手动添加一些配置来将这些页面也通过HTTPS保护起来

```yaml
eureka:
  instance:
    statusPageUrl: https://${eureka.hostname}/info
    healthCheckUrl: https://${eureka.hostname}/health
    homePageUrl: https://${eureka.hostname}/
```

注意，`eureka,hostname`是Eureka原生属性，只有新版本的Eureka才支持该属性。也可以使用Spring EL表达式代替`${eureka.instance.hostName}`

## 健康检查

默认情况下，Eureka通过客户端发来的心跳包来判断客户端是否在线。如果你不显式指定，客户端在心跳包中不会包含当前应用的健康数据(由Spring Boot Actuator提供)。这意味着只要客户端启动时完成了服务注册，那么该客户端在主动注销之前在Eureka中的状态会永远是`UP`状态。我们可以通过配置修改这一默认行为，即在客户端发送心跳包时会带上自己的健康信息。这样做的后果是只有当该服务的状态是`UP`时才能被访问，其它的任何状态都会导致该服务不能被调用

```yaml
eureka:
  client:
    healthcheck:
      enabled: true
```

如果你想对健康检查有更细粒度的控制，你可以自己实现`com.netflix.appinfo.HealthCheckHandler`接口

最好不要修改30s的默认心跳间隔，因为Server会使用这个时间数值来判断是否出现了大面积故障

## Eureka元数据说明

我们有必要花一些时间来了解一下Eureka的元数据，这样就可以添加一些自定义的数据以适应特定的业务场景。像主机名、IP地址、端口号、状态页url和健康检查url都是Eureka定义的标准元数据。这些元数据会被保存在Eureka Server的注册信息中，客户端会读取这些数据来向需要调用的服务直接发起连接。你可以使用以`eureka.instance.metadataMap`开头的参数来添加你自定义的元数据，所有客户端都会读取到该信息。通过这种方式你能给客户端自定义一些行为

## 使用EurekaClient对象

当添加了`@EnableDiscoveryClient`或`@EnableEurekaClient`注解后，你就可以在应用中使用`EurekaClient`对象来获取服务列表

```java
@Autowired
private EurekaClient discoveryClient;

public String serviceUrl() {
    InstanceInfo instance = discoveryClient.getNextServerFromEureka("STORES", false);
    return instance.getHomePageUrl();
}
```

不要在`@PostConstruct`或`@Scheduled`方法中使用`EurekaClient`。在`ApplicationContext`还没有完全启动时使用该对象会发生错误

## 使用Spring的DiscoveryClient对象

你没有必要直接使用Netflix原生的`EurekaClient`对象，在此基础上做一些封装使用起来会更方便。Spring Cloud支持`Feign`和`Spring RestTmpelate`，它们都可以使用服务的逻辑名而不是URL地址来查询服务。如果想给`Ribbon`手工指定服务列表，你可以将`<client>.ribbon.listOfServers`属性设为逗号分隔的物理地址或主机名, 参数中的`client`是服务id，即服务名。

你可以使用Spring提供的`DiscoveryClient`对象从而代码不会与Eureka紧耦合

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

## 为什么注册一个服务这么慢?

服务的注册涉及到心跳连接，默认为每30秒一次。只有当Eureka服务端和客户端本地缓存中的服务元数据相同时这个服务才能被其它客户端发现，这需要3个心跳周期。你可以通过参数`eureka.instance.leaseRenewalIntervalInSeconds`调整这个时间间隔来加快这个过程。在生产环境中你最好使用默认值，因为Eureka内部的某些计算依赖于该时间间隔

## 高可用, Zone 和 Region

Eureka把所有注册信息都放在内存中，所有注册过的客户端都会向Eureka发送心跳包来保持连接。客户端会有一份本地注册信息的缓存，这样就不需要每次远程调用时都向Eureka查询注册信息。

默认情况下，Eureka服务端自身也是个客户端，所以需要指定一个Eureka Server的URL作为"伙伴"(peer)。如果你没有提供这个地址，Eureka Server也能正常启动工作，但是在日志中会有大量关于找不到peer的错误信息

## Standalone模式

只要Eureka Server进程不会挂掉，这种集Server和Client于一身和心跳包的模式能让Standalone(单台)部署的Eureka Server非常容易进行灾难恢复。在 Standalone 模式中，可以通过下面的配置来关闭查找“伙伴”的行为

```yaml
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
```

注意，`serviceUrl`中的地址的主机名要与本地主机名相同

## "伙伴"感知

Eureka Server可以通过运行多个实例并相互指定为“伙伴”的方式来达到更高的高可用性。实际上这就是默认设置，你只需要指定“伙伴”的地址就可以了

```yaml
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
```

在上面这个例子中，我们通过使用不同`profile`配置的方式可以在本地运行两个Eureka Server。你可以通过修改`/etc/host`文件，使用上述配置在本地测试伙伴感特性。

你可以同时启动多个Eureka Server, 并通过伙伴配置使之围成一圈(相邻两个Server互为伙伴)，这些Server中的注册信息都是同步的。

如果对等点在物理上是分开的(在一个数据中心内或在多个数据中心之间)，那么系统原则上可以承受脑裂类型的故障

## 使用IP地址

有些时候你可能更倾向于直接使用IP地址定义服务而不是使用主机名。把`eureka.instance.preferIpAddress`参数设为`true`时，客户端在注册时就会使用自己的ip地址而不是主机名

## 附录

[官方中文文档](http://docs.springcloud.cn/user-guide/eureka/)

[微服务之服务发现 Eureka的介绍与使用](https://martian101.github.io/2017/04/20/%E5%BE%AE%E6%9C%8D%E5%8A%A1%E4%B9%8B%E6%9C%8D%E5%8A%A1%E5%8F%91%E7%8E%B0-Eureka%E7%9A%84%E4%BB%8B%E7%BB%8D%E4%B8%8E%E4%BD%BF%E7%94%A8/)

[Eureka学习文档资料](http://docs.springcloud.cn/user-guide/eureka/)
