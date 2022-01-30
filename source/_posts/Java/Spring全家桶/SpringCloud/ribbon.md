---
title: ribbon
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: SpringCloud
keywords: ribbon
excerpt: 什么是 Ribbon? 为什么要用 Ribbon?
date: 2022-01-28 00:05:54
thumbnailImage:
---
<!-- toc -->

## 概述

Spring Cloud Ribbon 是基于 Netflix Ribbon 实现的一套客户端负载均衡的工具，主要功能是**提供客户端的软件负载均衡算法和服务调用**。Ribbon 客户端组件提供一系列完善的配置项如连接超时，重试等。简单地说，就是在配置文件中列出 Load Balancer(简称LB) 后面所有的机器，Ribbon 会自动的帮助你基于某种规则(如简单轮询，随机连接等）去连接这些机器。我们很容易使用 Ribbon 实现自定义的负载均衡算法
{% alert info no-icon %}

:notebook: Ribbon目前也进入维护模式。Ribbon未来可能被Spring Cloud LoadBalacer 替代

{% endalert %}

:question:什么是 Load Banlance(负载均衡)?
{% alert success no-icon%}

简单的说就是将用户的请求平摊的分配到多个服务上，从而达到系统的HA (高可用)，常见的负载均衡有软件Nginx，LVS，硬件 F5 等
{%endalert%}

>想要详细了解负载均衡技术，可以查看这篇{% post_link 解决方案/负载均衡解决方案 文章%}

:vs: Ribbon 的负载均衡 VS Nginx 的负载均衡两者有什么区别？
{% alert success no-icon %}

- Nginx 的负载均衡是一种服务端负载均衡技术，客户端发送请求后，被服务端负载均衡器拦截，随后负载均衡器根据相应的负载均衡算法分发请求到具体服务器上处理请求。
- Ribbon 的负载均衡是一种客户端负载均衡技术，客户端想某一处发送请求时，会根据自身内部代码逻辑，已经做好了负载均衡的实现，直接访问到具体的服务。Ribbon 的客户端负载均衡配置就处于微服务的消费者中，消费者根据代码中自身拥有的负载均衡算法使用 `restTemplate`进行远程调用。

{% endalert %}
从整体的请求过程可以明显看出服务端负载均衡和客户端负载均衡的差别：

```
服务端负载均衡： 客户端请求 ===> 负载均衡器 ===> 服务器
客户端负载均衡： 客户端请求 ===> 服务器
```
{% alert info no-icon %}

服务端负载均衡就是通过一台服务器达到负载均衡效果，客户端负载均衡时通过自身就能够达到负载均衡，并不需要其他服务器；更准确的说，对于服务端的负载均衡技术，所有服务器的清单都是保存在负载均衡器上，负载均衡器用于维护服务器的增加或减少、而对于客户端负载均衡技术，客户端拥有所有服务器的清单，所以客户端能够越过负载均衡器，自身完成负载均衡技术

{% endalert %}
根据最终负载均衡所在的位置，也可以将负载均衡技术分为两种：集中式负载均衡以及进程内负载均衡

### 集中式 LB

集中式 LB 即在服务的消费方和提供方之间使用独立的LB设施(可以是硬件，如 F5, 也可以是软件，如nginx)，由该设施负责把访问请求通过某种策略转发至服务的提供方

### 进程内 LB

进程内LB，将 LB 逻辑集成到消费方，消费方从服务注册中心获知有哪些地址可用，然后自己再从这些地址中选择出一个合适的服务器。Ribbon 就属于111进程内 LB，它只是一个类库，集成于消费方进程，消费方通过它来获取到服务提供方的地址
{% alert info no-icon %}

简单的理解， Ribbion 就是负载均衡和 RestTemplate 调用

{% endalert %}

## Ribbon 的负载均衡

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/springcloud/spirngcloud-ribbon-requestflow.png)

:boat: Ribbon在工作时分成两步：

1. 先选择EurekaServer ,它优先选择在同一个区域内负载较少的server。

2. 根据用户指定的策略，在从server取到的服务注册列表中选择一个地址。

其中Ribbon提供了多种策略：比如轮询、随机和根据响应时间加权。

```xml
<dependency>
    <groupld>org.springframework.cloud</groupld>
    <artifactld>spring-cloud-starter-netflix-ribbon</artifactid>
</dependency>
```

spring-cloud-starter-netflix-eureka-client 自带了 spring-cloud-starter-ribbon 引用

## Ribbon 默认自带的负载规则

Ribbon 中的每一种负载均衡规则都对应着一个类，各个算法具体的含义如下表所示：

|            类             |                             含义                             |
| :-----------------------: | :----------------------------------------------------------: |
|      RoundRobinRule       |                           轮询规则                           |
|        RandomRule         |                             随机                             |
|         RetryRule         | 先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试 |
| WeightedResponseTimeRule  | 对RoundRobinRule的扩展，响应速度越快的实例选择权重越大，越容易被选择 |
|     BestAvailableRule     | 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务 |
| AvailabilityFilteringRule |            先过滤掉故障实例，再选择并发较小的实例            |
|     ZoneAvoidanceRule     | 默认规则,复合判断server所在区域的性能和server的可用性选择服务器 |

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/springcloud/springcloud-ribbon-iruler.png)

 如果想要自定义实现某个算法，只需要实现`IRule` 接口中的部分方法即可

## Ribbon 负载规则替换

:notes: 官方文档明确给出了警告： 自定义的 Ribbon 负载均衡类不能放在`@componentScan`所扫描的当前包及其子包下，否则这个自定义的配置类就会被所有的 Ribbon 客户端所共享，达不到特殊化定制的目的。简而言之，就是不要将 Ribbon 配置类与主启动类放在同一个包中

```java
//在另一个子包中向 Spring 中注入 IRule 组件
@Configuration
public class MySelfRule {
	@Bean
	public IRule myRule() {
		return new RandomRule();
	}
}
```

```java
@SpringBootApplication
@EnableEurekaClient
//向 spring boot 声明当前微服务需要使用的 ribbon 规则
@RibbonClient(name = "CLOUD-PAYMENT-SERVICE", configuration = MySelfRule.class)
public class OrderMain80 {
	public static void main(String[] args) {
		SpringApplication.run(OrderMain80.class, args);
	}
}
```
{% alert success no-icon %}

:notes: 由于最终使用的是`RestTemplate`进行远程调用，所以还需要对注入的`RestTemplate	`进行配置

{% endalert %}

```java
@Configuration
public class ApplicationContextConfig {
	@Bean
	@LoadBalanced
	//声明 resttemplate 使用 ribbon 负载均衡
	public RestTemplate getRestTemplate() {
		return new RestTemplate();
	}
}
```

## Ribbon 默认负载轮询算法原理
{% alert success no-icon %}

默认负载轮训算法: rest接口第几次请求数 % 服务器集群总数量 = 实际调用服务器位置下标，每次服务重启动后rest接口计数从1开始

{% endalert %}
{% codeblock IRule.java lang:java %}

public interface IRule{
    /*
     * choose one alive server from lb.allServers or
     * lb.upServers according to key
     * 
     * @return choosen Server object. NULL is returned if none
     *  server is available 
     */

    //重点关注这方法
    public Server choose(Object key);
    
    public void setLoadBalancer(ILoadBalancer lb);
    
    public ILoadBalancer getLoadBalancer();    
}

{% endcodeblock %}

{% codeblock RoundRobinRule.java lang:java %}
ublic class RoundRobinRule extends AbstractLoadBalancerRule {

    private AtomicInteger nextServerCyclicCounter;
    private static final boolean AVAILABLE_ONLY_SERVERS = true;
    private static final boolean ALL_SERVERS = false;
    
    private static Logger log = LoggerFactory.getLogger(RoundRobinRule.class);
    
    public RoundRobinRule() {
        nextServerCyclicCounter = new AtomicInteger(0);
    }
    
    public RoundRobinRule(ILoadBalancer lb) {
        this();
        setLoadBalancer(lb);
    }
    
    //重点关注这方法。
    public Server choose(ILoadBalancer lb, Object key) {
        if (lb == null) {
            log.warn("no load balancer");
            return null;
        }
    
        Server server = null;
        int count = 0;
        while (server == null && count++ < 10) {
            List<Server> reachableServers = lb.getReachableServers();
            List<Server> allServers = lb.getAllServers();
            int upCount = reachableServers.size();
            int serverCount = allServers.size();
    
            if ((upCount == 0) || (serverCount == 0)) {
                log.warn("No up servers available from load balancer: " + lb);
                return null;
            }
    
            int nextServerIndex = incrementAndGetModulo(serverCount);
            server = allServers.get(nextServerIndex);
    
            if (server == null) {
                /* Transient. */
                Thread.yield();
                continue;
            }
    
            if (server.isAlive() && (server.isReadyToServe())) {
                return (server);
            }
    
            // Next.
            server = null;
        }
    
        if (count >= 10) {
            log.warn("No available alive servers after 10 tries from load balancer: "
                    + lb);
        }
        return server;
    }
    
    /**
     * Inspired by the implementation of {@link AtomicInteger#incrementAndGet()}.
     *
     * @param modulo The modulo to bound the value of the counter.
     * @return The next value.
     */
    private int incrementAndGetModulo(int modulo) {
        for (;;) {
            int current = nextServerCyclicCounter.get();
            int next = (current + 1) % modulo;//求余法
            if (nextServerCyclicCounter.compareAndSet(current, next))
                return next;
        }
    }
    
    @Override
    public Server choose(Object key) {
        return choose(getLoadBalancer(), key);
    }
    
    @Override
    public void initWithNiwsConfig(IClientConfig clientConfig) {
    }
}
{% endcodeblock %}

## 附录
[服务端负载均衡和客户端负载均衡（Ribbon）的区别](https://www.jianshu.com/p/22b2c362b973)
[客户端负载均衡与服务端负载均衡](https://blog.csdn.net/u014401141/article/details/78676296)