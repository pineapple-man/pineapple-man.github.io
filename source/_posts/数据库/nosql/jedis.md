---
title: Jedis
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-01-12 21:33:23
thumbnailImage:
categories: 中间件
tags: Redis
keywords: Jedis
excerpt: 本文主要记录 Jedis 的常见用法
---
<!-- toc -->
## 概述

jedis 是 java redis 的简写，目的是通过 java 程序进行 redis 相关操作，如果想要使用此中间价，只需要向项目中引入以下依赖即可。

```xml
<dependency>
	<groupId>redis.clients</groupId>
	<artifactId>jedis</artifactId>
	<version>2.1.0</version>
</dependency>
```

## 联通测试
引入依赖之后，仅需要通过如下步骤即可测试 Redis 的联通性，查看项目是否使用 Jedis 成功
```java
public class TestRedis {
   public static void main(String[] args) {
      String address = "127.0.0.1";
      String port = "6379";
      Jedis jedis = new Jedis(address, Integer.parseInt(port));
      System.out.println(jedis.ping());
       //输出PONG，redis连通成功
   }
}
```
## Jedis 主从复制

```java
import redis.clients.jedis.Jedis;

public class TestMS {
	public static void main(String[] args) {
		Jedis jedis_M = new Jedis("127.0.0.1", 6379);
		Jedis jedis_S = new Jedis("127.0.0.1", 6380);

		jedis_S.slaveof("127.0.0.1", 6379);

		jedis_M.set("class", "1122V2");

		String result = jedis_S.get("class");//可能有延迟，需再次启动才能使用
		System.out.println(result);
	}
}
```

## Jedis 连接池

```java
class JedisPollUtils {
    //懒汉式单例模式创建连接池
   private static volatile JedisPool jedisPool = null;
   
   private JedisPollUtils() {
   }
   
   public static JedisPool getJedisPoolInstance() {
      if (jedisPool == null) {
         synchronized (JedisPollUtils.class) {
            if (null == jedisPool) {
               JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
               jedisPoolConfig.setMaxTotal(2);
               jedisPoolConfig.setMaxIdle(32);
               jedisPoolConfig.setMaxWaitMillis(100 * 1000);
               jedisPoolConfig.setTestOnBorrow(true);
               jedisPool = new JedisPool(jedisPoolConfig, "127.0.0.1", 6379);
            }
         }
      }
      return jedisPool;
   }
   
   public static void release(Jedis jedis) {
      if (null != jedis) {
          //回收连接，会将当前连接分配给另一个线程
         jedis.close();
      }
   }
}
```

## 连接池常用参数

|       参数       |                             含义                             |
| :--------------: | :----------------------------------------------------------: |
|   **MaxTotal**   | 控制一个pool可分配多少个jedis实例，通过pool.getResource()来获取；如果赋值为-1，则表示不限制； |
|   **maxIdle**    |     控制一个pool最多有多少个状态为idle(空闲)的jedis实例      |
|   **maxWait**    | 表示当borrow一个jedis实例时，最大的等待时间，如果超过等待时间，则直接抛JedisConnectionException |
| **testOnBorrow** | 获得一个jedis实例的时候是否检查连接可用性（ping()）；如果为true，则得到的jedis实例均是可用的 |
| **testOnReturn** |  return 一个jedis实例给pool时，是否检查连接可用性（ping()）  |

## 附录

[JedisAPI整理](https://blog.csdn.net/fanbaodan/article/details/89047909)

