---
title: Redis 基础
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 数据库
tags: Redis
keywords: Redis
excerpt: Redis 是常见的 NoSQL 数据库，本文记录使用 Redis 常见的操作
date: 2022-01-01 21:29:44
thumbnailImage:
---

<!-- toc -->

## 概述

Redis：REmote DIctionary Server（远程字典服务器）是一个高性能的( key / value )分布式{% hl_text red %} 内存数据库{% endhl_text %}，基于内存运行并支持持久化的 NoSQL 数据库，是当前最热门的 NoSql 数据库之一

:thinking: Redis 能干嘛？
{% alert warning no-icon%}

- 内存存储和持久化：redis 支持异步的将内存中的数据写到硬盘上，同时不影响服务
- 取最新 N 个数据的操作，如：可以将最新的 10 条评论的 ID 放在 Redis 的 List 集合中
- 模拟类似于 HttpSession 这种需要需要设定过期时间的功能
- 发布、订阅消息系统
- 定时器、计数器
  {%endalert%}

:thinking:学习 Redis 的步骤
{% alert info no-icon%}

- 数据类型、基本操作和配置
- 持久化和复制，RDB/AOF
- 事务的控制
- 复制（主从关系）
  {%endalert%}

## 安装

根据操作系统不同， Redis 的安装分为两种：Linux 与 Windows

### Linux 下安装 Redis

{% hl_text red %}下载压缩包 {% endhl_text %}

```shell
wget http://download.redis.io/releases/redis-6.0.8.tar.gz
tar xzf redis-6.0.8.tar.gz
cd redis-6.0.8
make
```

{% alert warning no-icon%}
执行完 **make** 命令后，redis-6.0.8 的 **src** 目录下会出现编译后的 redis 服务程序 redis-server，还有用于测试的客户端程序 redis-cli：
{%endalert%}

```shell
cd src
redis-server #使用默认配置
redis-server /SOMEWHERE/CONFIG/FILE #使用指定文件作为启动配置文件
```

使用`redis-cli`命令可以访问对应地址上的 redis 服务

### Windows 下安装 Redis

{% alert warning no-icon%}

1. [Redis 官网](https://www.oschina.net/action/GoToLink?url=https%3A%2F%2Fredis.io%2F)下载对应的压缩包，解压
2. 输入`redis-server.exe redis.windows.conf`，打开 Redis 服务端
   {%endalert%}

至此`redis-server`启动成功，需要使用`redis-cli`工具去连接服务端，输入`redis-cli.exe -h 127.0.0.1 -p 6379`命令，看到命令提示符改变，说明 redis 安装成功，并且已经访问了 redis 提供的服务

## 杂项知识

- 在`redis`安装目录下`redis-benchmark.exe`将会测试 redis 在机器运行的效能
- redis 是单进程

  - 单进程模型来处理客户端的请求。对读写等事件的响应式通过对`epoll`函数的包装来做到的，Redis 的实际处理速度完全依靠主进程的执行效率
  - `Epoll`是 Linux 内核为处理大批量文件描述符而做了改进的 epoll，是 Linux 下多路复用 IO 接口`select/poll`的增强版本，它能显著提供程序在大量并发连接中只有少量活跃的情况下的系统 CPU 利用率

- 和 MySQL 启动拥有默认数据库相同，**Redis 启动默认有 16 个数据库**，类似数组下标从零开始，初始默认使用零号库，可以在配置文件中配置
- Redis 索引都是从零开始
- 默认端口是：**6379**

|    命令    |            含义             |
| :--------: | :-------------------------: |
|  `select`  |       命令切换数据库        |
|  `dbsize`  | 查看当前数据库的`key`的数量 |
| `flushdb`  |         清空当前库          |
| `flushall` |         清空全部库          |

:notes:Redis 统一密码管理，16 个库都是同样的密码，要么都 OK 要么一个也连接不上

## Key 关键字

与 HashMap 中 Key 含义相同

|                   命令                    |                                                             描述                                                              |
| :---------------------------------------: | :---------------------------------------------------------------------------------------------------------------------------: |
|                  DEL key                  |                                                该命令用于在 key 存在时删除 key                                                |
|                 DUMP key                  |                                              序列化给定 key ，并返回被序列化的值                                              |
|                EXISTS key                 |                                                     检查给定 key 是否存在                                                     |
|            EXPIRE key seconds             |                                                为给定 key 设置过期时间，以秒计                                                |
|          EXPIREAT key timestamp           | EXPIREAT 的作用和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp) |
|         PEXPIRE key milliseconds          |                                                  设置 key 的过期时间以毫秒计                                                  |
|   PEXPIREAT key milliseconds-timestamp    |                                      设置 key 过期时间的时间戳(unix timestamp) 以毫秒计                                       |
|               KEYS pattern                |                                             查找所有符合给定模式( pattern)的 key                                              |
|                MOVE key db                |                                         将当前数据库的 key 移动到给定的数据库 db 当中                                         |
|                PERSIST key                |                                              移除 key 的过期时间，key 将持久保持                                              |
|                 PTTL key                  |                                             以毫秒为单位返回 key 的剩余的过期时间                                             |
|                  TTL key                  |                                            以秒为单位，返回给定 key 的剩余生存时间                                            |
|                 RANDOMKEY                 |                                                从当前数据库中随机返回一个 key                                                 |
|             RENAME key newkey             |                                                        修改 key 的名称                                                        |
|            RENAMENX key newkey            |                                          仅当 newkey 不存在时，将 key 改名为 newkey                                           |
| SCAN cursor [MATCH pattern] [COUNT count] |                                                    迭代数据库中的数据库键                                                     |
|                 TYPE key                  |                                                   返回 key 所储存的值的类型                                                   |

## Redis 常用数据类型

Redis 常用数据结构如下图，具体每个类型的特点在下文进行阐述

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/202112292135049.png %}
{% alert info no-icon%}
上述所属的数据类型表示，Redis 中 value 的类型，因为 Redis 是 key-value 的数据库，key 必然是 String 类型，所以 Value 可以拥有不同的类型
{%endalert%}

### String

{% alert success no-icon%}

- String 是 redis 最基本的类型，一个 key 对应一个 value，一个 value 最多可以是 **512M**
- String 类型是二进制安全的，意味着：redis 的 string 可以包含任何数据，比如：jpg 图片或者序列化的对象

{%endalert%}

|              命令              |                                                描述                                                 |
| :----------------------------: | :-------------------------------------------------------------------------------------------------: |
|         SET key value          |                                          设置指定 key 的值                                          |
|            GET key             |                                          获取指定 key 的值                                          |
|     GETRANGE key start end     |                                     返回 key 中字符串值的子字符                                     |
|        GETSET key value        |                      将给定 key 的值设为 value ，并返回 key 的旧值(old value)                       |
|       GETBIT key offset        |                         对 key 所储存的字符串值，获取指定偏移量上的位(bit)                          |
|       MGET key1 [key2…]        |                                  获取所有(一个或多个)给定 key 的值                                  |
|    SETBIT key offset value     |                      对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)                       |
|    SETEX key seconds value     |                将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)                 |
|        SETNX key value         |                                  只有在 key 不存在时设置 key 的值                                   |
|   SETRANGE key offset value    |                  用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始                   |
|           STRLEN key           |                                   返回 key 所储存的字符串值的长度                                   |
|  MSET key value [key value …]  |                                   同时设置一个或多个 key-value 对                                   |
| MSETNX key value [key value …] |                   同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在                    |
| PSETEX key milliseconds value  | 这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位 |
|            INCR key            |                                      将 key 中储存的数字值增一                                      |
|      INCRBY key increment      |                           将 key 所储存的值加上给定的增量值（increment）                            |
|   INCRBYFLOAT key increment    |                         将 key 所储存的值加上给定的浮点增量值（increment）                          |
|            DECR key            |                                      将 key 中储存的数字值减一                                      |
|      DECRBY key decrement      |                             key 所储存的值减去给定的减量值（decrement）                             |
|        APPEND key value        |  如果 key 已经存在并且是一个字符串， APPEND 命令将指定的 value 追加到该 key 原来值（value）的末尾   |

### Hash

{% alert success no-icon%}

- redis hash 是一个键值对集合
- hash 是一个 String 类型的 field 和 value 的映射表，hash 特别适合用于存储对象
- 类似于 Java 中的`Map<String,Map<String,Object>>`
  {%endalert%}

|                      命令                      |                         描述                          |
| :--------------------------------------------: | :---------------------------------------------------: |
|            HDEL key field1 [field2]            |               删除一个或多个哈希表字段                |
|               HEXISTS key field                |         查看哈希表 key 中，指定的字段是否存在         |
|                 HGET key field                 |            获取存储在哈希表中指定字段的值             |
|                  HGETALL key                   |         获取在哈希表中指定 key 的所有字段和值         |
|          HINCRBY key field increment           |  为哈希表 key 中的指定字段的整数值加上增量 increment  |
|        HINCRBYFLOAT key field increment        | 为哈希表 key 中的指定字段的浮点数值加上增量 increment |
|                   HKEYS key                    |                获取所有哈希表中的字段                 |
|                    HLEN key                    |                获取哈希表中字段的数量                 |
|           HMGET key field1 [field2]            |                 获取所有给定字段的值                  |
|    HMSET key field1 value1 [field2 value2 ]    |  同时将多个 field-value (域-值)对设置到哈希表 key 中  |
|              HSET key field value              |      将哈希表 key 中的字段 field 的值设为 value       |
|             HSETNX key field value             |     只有在字段 field 不存在时，设置哈希表字段的值     |
|                   HVALS key                    |                  获取哈希表中所有值                   |
| HSCAN key cursor [MATCH pattern] [COUNT count] |                 迭代哈希表中的键值对                  |

### List

{% alert success no-icon%}

- redis 列表是简单的字符串列表，按照插入顺序排序，可以添加一个元素到列表的头部或者尾部
- 单 key 多 Value,Value 之间通过链表连接在一起，`Map<String,LinkedList<String>>`
  {%endalert%}

|                 命令                  |                                                           描述                                                            |
| :-----------------------------------: | :-----------------------------------------------------------------------------------------------------------------------: |
|       BLPOP key1 [key2] timeout       |                  移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止                  |
|      BRPOP key1 [key2 ] timeout       |                 移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止                 |
| BRPOPLPUSH source destination timeout | 从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止 |
|           LINDEX key index            |                                                 通过索引获取列表中的元素                                                  |
| LINSERT key BEFORE/AFTER pivot value  |                                               在列表的元素前或者后插入元素                                                |
|               LLEN key                |                                                       获取列表长度                                                        |
|               LPOP key                |                                                移出并获取列表的第一个元素                                                 |
|       LPUSH key value1 [value2]       |                                               将一个或多个值插入到列表头部                                                |
|           LPUSHX key value            |                                              将一个值插入到已存在的列表头部                                               |
|         LRANGE key start stop         |                                                 获取列表指定范围内的元素                                                  |
|         LREM key count value          |                                                       移除列表元素                                                        |
|         LSET key index value          |                                                 通过索引设置列表元素的值                                                  |
|         LTRIM key start stop          |             对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除              |
|               RPOP key                |                                        移除列表的最后一个元素，返回值为移除的元素                                         |
|     RPOPLPUSH source destination      |                                 移除列表的最后一个元素，并将该元素添加到另一个列表并返回                                  |
|       RPUSH key value1 [value2]       |                                                 在列表中添加一个或多个值                                                  |
|           RPUSHX key value            |                                                   为已存在的列表添加值                                                    |

{% alert info no-icon%}

- List 是一个双向字符串链表,left,right 都可以插入
- 如果键不存在,创建新的链表
- 如果键已存在,新增内容
- 如果值全移除,对应的键也就消失了
- 链表的操作无论是头和尾效率都极高,但如果是对中间元素进行操作,效率就不太理想
  {%endalert%}

### Set

{% alert success no-icon%}

redis 的 Set 是 String 类型的无序集合，单 key 多 value,类似于`Map<String,Set<String>>`
{%endalert%}

|                      命令                      |                        描述                         |
| :--------------------------------------------: | :-------------------------------------------------: |
|           SADD key member1 [member2]           |              向集合添加一个或多个成员               |
|                   SCARD key                    |                  获取集合的成员数                   |
|               SDIFF key1 [key2]                |               返回给定所有集合的差集                |
|       SDIFFSTORE destination key1 [key2]       |    返回给定所有集合的差集并存储在 destination 中    |
|               SINTER key1 [key2]               |               返回给定所有集合的交集                |
|      SINTERSTORE destination key1 [key2]       |    返回给定所有集合的交集并存储在 destination 中    |
|              SISMEMBER key member              |        判断 member 元素是否是集合 key 的成员        |
|                  SMEMBERS key                  |                返回集合中的所有成员                 |
|        SMOVE source destination member         | 将 member 元素从 source 集合移动到 destination 集合 |
|                    SPOP key                    |           移除并返回集合中的一个随机元素            |
|            SRANDMEMBER key [count]             |             返回集合中一个或多个随机数              |
|           SREM key member1 [member2]           |              移除集合中一个或多个成员               |
|               SUNION key1 [key2]               |               返回所有给定集合的并集                |
|      SUNIONSTORE destination key1 [key2]       |     所有给定集合的并集存储在 destination 集合中     |
| SSCAN key cursor [MATCH pattern] [COUNT count] |                  迭代集合中的元素                   |

### Zset

{% alert success no-icon%}

- Redis zset 和 set 一样也是 string 类型的集合，且不允许重复的成员
- 每个元素都会关联一个 double 类型的分数，redis 通过分数为集合中的成员进行从小到大的排序
- zset 的成员是唯一的，但分数(score)却可以重复
  {%endalert%}

|                      命令                      |                                描述                                 |
| :--------------------------------------------: | :-----------------------------------------------------------------: |
|    ZADD key score1 member1 [score2 member2]    |       向有序集合添加一个或多个成员，或者更新已存在成员的分数        |
|                   ZCARD key                    |                        获取有序集合的成员数                         |
|               ZCOUNT key min max               |                计算在有序集合中指定区间分数的成员数                 |
|          ZINCRBY key increment member          |            有序集合中对指定成员的分数加上增量 increment             |
|  ZINTERSTORE destination numkeys key [key …]   | 计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中 |
|             ZLEXCOUNT key min max              |               在有序集合中计算指定字典区间内成员数量                |
|       ZRANGE key start stop [WITHSCORES]       |              通过索引区间返回有序集合指定区间内的成员               |
|  ZRANGEBYLEX key min max [LIMIT offset count]  |                   通过字典区间返回有序集合的成员                    |
| ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT] |                通过分数返回有序集合指定区间内的成员                 |
|                ZRANK key member                |                    返回有序集合中指定成员的索引                     |
|           ZREM key member [member …]           |                   移除有序集合中的一个或多个成员                    |
|           ZREMRANGEBYLEX key min max           |               移除有序集合中给定的字典区间的所有成员                |
|         ZREMRANGEBYRANK key start stop         |               移除有序集合中给定的排名区间的所有成员                |
|          ZREMRANGEBYSCORE key min max          |               移除有序集合中给定的分数区间的所有成员                |
|     ZREVRANGE key start stop [WITHSCORES]      |        返回有序集中指定区间内的成员，通过索引，分数从高到低         |
|   ZREVRANGEBYSCORE key max min [WITHSCORES]    |         返回有序集中指定分数区间内的成员，分数从高到低排序          |
|              ZREVRANK key member               | 返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序  |
|               ZSCORE key member                |                     返回有序集中，成员的分数值                      |
|  ZUNIONSTORE destination numkeys key [key …]   |        计算给定的一个或多个有序集的并集，并存储在新的 key 中        |
| ZSCAN key cursor [MATCH pattern] [COUNT count] |           迭代有序集合中的元素（包括元素成员和元素分值）            |

:books:更多详细的用法可以访问[Redis 命令参考](http://redisdoc.com/)

## Redis 配置文件

Redis 的配置文件位于 Redis 安装目录下,文件名为 `redis.conf`,可以通过 CONFIG 命令查看或设置配置项

### 语法

```shell
redis 127.0.0.1:6379> CONFIG GET CONFIG_SETTING_NAME
redis 127.0.0.1:6379> CONFIG GET loglevel

1)"loglevel"
2)"notice"
```

### 参数说明

`redis.conf`配置项说明如下

|                          配置项                          |                                                                                                                                           说明                                                                                                                                           |
| :------------------------------------------------------: | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: |
|                      `daemonize no`                      |                                                                                     Redis 默认不是以守护进程的方式运行,可以通过该配置项修改,使用 yes 启用守护进程(Windows 不支持守护线程的配置为 no)                                                                                     |
|               `pidfile /var/run/redis.pid`               |                                                                                                      当 Redis 以守护进程方式运行时,Redis 默认会把 pid 写入`/var/run/redis.pid`文件                                                                                                       |
|                       `port 6379`                        |                                                                                                                           指定 Redis 监听端口，默认端口为 6379                                                                                                                           |
|                     `bind 127.0.0.1`                     |                                                                                                                                      绑定的主机地址                                                                                                                                      |
|                      `timeout 300`                       |                                                                                                               当客户端闲置多长秒后关闭连接，如果指定为 0 ，表示关闭该功能                                                                                                                |
|                    `loglevel notice`                     |                                                                                                 指定日志记录级别，Redis 总共支持四个级别：debug、verbose、notice、warning，默认为 notice                                                                                                 |
|                     `logfile stdout`                     |                                                                            日志记录方式，默认为标准输出，如果配置 Redis 为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给 /dev/null                                                                             |
|                      `databases 16`                      |                                                                                                       设置数据库的数量，默认数据库为 0，可以使用 SELECT 命令在连接上指定数据库 id                                                                                                        |
|                `save <seconds> <changes>`                |                                                                                                         规定的时间内,存在多少次更新操作,就将数据同步到数据文件,可以多个条件配合                                                                                                          |
|                   `rdbcompression yes`                   |                                                                          指定存储至本地数据库时是否压缩数据，默认为 yes，Redis 采用 LZF 压缩，如果为了节省 CPU 时间，可以关闭该选项，但会导致数据库文件变的巨大                                                                          |
|                  `dbfilename dump.rdb`                   |                                                                                                                         指定本地数据库文件名，默认值为 dump.rdb                                                                                                                          |
|                         `dir ./`                         |                                                                                                                                  指定本地数据库存放目录                                                                                                                                  |
|            `slaveof <masterip> <masterport>`             |                                                                                       设置当本机为 slave 服务时，设置 master 服务的 IP 地址及端口，在 Redis 启动时，它会自动从 master 进行数据同步                                                                                       |
|              `masterauth <master-password>`              |                                                                                                               当 master 服务设置了密码保护时，slav 服务连接 master 的密码                                                                                                                |
|                  `requirepass foobared`                  |                                                                                            设置 Redis 连接密码，如果配置了连接密码，客户端在连接 Redis 时需要通过 AUTH 命令提供密码，默认关闭                                                                                            |
|                     `maxclients 128`                     |               设置同一时间最大客户端连接数，默认无限制，Redis 可以同时打开的客户端连接数为 Redis 进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis 会关闭新的连接并向客户端返回 max number of clients reached 错误信息                |
|                   `maxmemory <bytes>`                    |        指定 Redis 最大内存限制，Redis 在启动时会把数据加载到内存中，达到最大内存后，Redis 会先尝试清除已到期或即将到期的 Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis 新的 vm 机制，会把 Key 存放内存，Value 会存放在 swap 区         |
|                     `appendonly no`                      |                      指定是否在每次更新操作后进行日志记录，Redis 在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no                      |
|             `appendfilename appendonly.aof`              |                                                                                                                        指定更新日志文件名，默认为 appendonly.aof                                                                                                                         |
|                  `appendfsync everysec`                  |                                  指定更新日志条件，共有 3 个可选值：<br /> no：表示等操作系统进行数据缓存同步到磁盘（快） <br />always：表示每次更新操作后手动调用 fsync() 将数据写到磁盘（慢，安全） <br />everysec：表示每秒同步一次（折中，默认值）                                   |
|                     `vm-enabled no`                      |                                                            指定是否启用虚拟内存机制，默认值为 no，简单的介绍一下，VM 机制将数据分页存放，由 Redis 将访问量较少的页即冷数据 swap 到磁盘上，访问多的页面由磁盘自动换出到内存中                                                             |
|              `vm-swap-file /tmp/redis.swap`              |                                                                                                           虚拟内存文件路径，默认值为 /tmp/redis.swap，不可多个 Redis 实例共享                                                                                                            |
|                    `vm-max-memory 0`                     |                                             将所有大于 vm-max-memory 的数据存入虚拟内存，无论 vm-max-memory 设置多小，所有索引数据(key)都是内存存储的，也就是说，当 vm-max-memory 设置为 0 的时候，其实是所有 value 都存在于磁盘。默认值为 0                                             |
|                    `vm-page-size 32`                     | Redis swap 文件分成了很多的 page，一个对象可以保存在多个 page 上面，但一个 page 上不能被多个对象共享，vm-page-size 是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page 大小最好设置为 32 或者 64bytes；如果存储很大大对象，则可以使用更大的 page，如果不确定，就使用默认值 |
|                   `vm-pages 134217728`                   |                                                                 设置 swap 文件中的 page 数量，由于页表（一种表示页面空闲或使用的 bitmap）是在放在内存中的，在磁盘上每 8 个 pages 将消耗 1byte 的内存(每 bit 表示一个页)                                                                  |
|                    `vm-max-threads 4`                    |                                                                       设置访问 swap 文件的线程数,最好不要超过机器的核数,如果设置为 0,那么所有对 swap 文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为 4                                                                        |
|                   `glueoutputbuf yes`                    |                                                                                                             设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启                                                                                                             |
| `hash-max-zipmap-entries 64` `hash-max-zipmap-value 512` |                                                                                                          指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希                                                                                                          |
|                  `activerehashing yes`                   |                                                                                                                             指定是否激活重置哈希，默认为开启                                                                                                                             |
|              `include /path/to/local.conf`               |                                                                                  指定包含其它的配置文件，可以在同一主机上多个 Redis 实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件                                                                                   |

Redis 的默认配置文件中提供了三个 save 配置条件
{% alert warning no-icon %}

```shell
save 900 1    ## 900秒内发生一次更改,则保存
save 300 10   ## 300秒内发生十次更改,则保存
save 60 1000  ## 60秒内发生一千次更改,则保存
```

{% endalert %}

## 持久化 Redis

Redis 是内存数据库，当物理机关闭或者宕机之后，内存中的数据必然会消失，所以就需要对 Redis 中的数据进行持久化存储，RDB 就是 Redis 持久化存储的一种方式

### RDB

:question:RDB 是什么?
{% alert success no-icon %}

RDB 是指：在指定的时间间隔内将内存中的数据集快照写入磁盘(Snapshot),恢复时将快照文件直接读到内存里

{% endalert %}

:question:RDB 是如何进行的？
{% alert success no-icon %}

- Redis 会单独创建(fork)一个子进程进行持久化，fork 通过复制一个与当前进程一样的子进程,子进程的所有数据(变量、环境变量、程序计数器等)数值都和原进程一样,但是是一个全新的进程,并作为父进程的子进程存在，单独进行持久化工作，并不影响当前工作主线程
- 先将数据写入到一个临时文件中，待持久化过程都结束了,再用这个临时文件替换上次持久化好的文件

{% endalert %}

:sparkles:RDB 的配置
{% alert success no-icon %}

- 相关配置在配置文件(`redis.conf`)中搜寻`#### SNAPSHOTTING ##`
- rdb 默认保存在`dump.rdb`文件，配置文件中的配置是`dbfilename dump.rdb`

{% endalert %}

#### 触发 RDB 快照

在配置文件中开启 RDB 之后，并不存在备份文件，则自动触发快照

命令方式触发

|    命令    |                                                        含义                                                        |
| :--------: | :----------------------------------------------------------------------------------------------------------------: |
|   `save`   |                                     save 时只管保存，其他不管，全部进程会阻塞                                      |
|  `bgsave`  | Redis 会在后台异步进行快照操作，同时主线程还可以响应客户端请求，可以通过`lastsave`命令获取最后一次成功执行快照时间 |
| `flushall` |                               强行清空 Redis 中的数据，并产生一个空的`dump.rdb`文件                                |

#### 快照恢复

将备份文件（`dump.rdb`）移动到 redis 安装目录并重新启动服务即可

{% alert warning no-icon %}

使用命令`127.0.0.1:6379> CONFIG GET dir`redis 将返回结果，用于查看 Redis 的安装目录

{% endalert %}

#### 停止 RDB

动态停止所有 RDB 保存规则：`redis-cli config set save ""`

#### 总结

RDB 的作用如下图所示：

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/202112292136744.png %}

:+1:RDB 优点如下：
{% alert info no-icon %}

- 适合大规模的数据恢复
- 对数据完整性和一致性要求不高
- 整个过程中,主进程是不进行任何 IO 操作的;这就确保了极高的性能.
- 如果需要进行大规模数据的恢复,且对于数据恢复的完整性不是非常敏感,那么 RDB 方式要比 AOF 方式更加的高效.

{% endalert %}

:persevere:RDB 缺点如下：
{% alert info no-icon %}

- 在一定间隔时间做一次备份，容易丢失最后一次快照后的所有修改
- Fork 的时候，内存中的数据被克隆了一份，将会造成内存占用膨胀现象
- RDB 需要经常`fork`子进程来保存数据集在硬盘上，当数据集比较大的时候`fork`的过程时非常耗时的，可能会导致`redis`在一些毫秒级不能响应客户端请求

{% endalert %}

### AOF（Append Only File）

{% alert success no-icon %}

AOF 是以日志的形式来记录每个写操作，将 Redis 执行过的所有写指令记录下来（读操作不记录），只能够追加文件但不可以改写文件，redis 启动之初会读取该文件重新构建数据

{% endalert %}

:question:AOF 是如何进行的？
{% alert success no-icon %}

redis 重启后就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作

{% endalert %}

:sparkles:AOF 配置
{% alert success no-icon %}

- 在配置文件中搜索`#### APPEND ONLY MODE ###`，默认关闭（`appendonly no`）
- AOF 保存的是`appendonly.aof`文件（`appendfilename "appendonly.aof"`）

{% endalert %}

#### AOF 操作

|                 操作                 |     含义      |
| :----------------------------------: | :-----------: |
| `redis-check-aof [--fix] <file.aof>` | 修复 aof 文件 |

#### rewrite

:question: rewrite 是什么？
{% alert success no-icon %}

AOF 采用文件追加方式，文件会越来越大，为避免出现此种情况，新增了重写机制，当 AOF 文件的大小超过所设定的阈值时，Redis 就会启动 AOF 文件的内容压缩，只保留可以恢复数据的最小指令集，可以使用命令`bgrewriteaof`

{% endalert %}

:sparkles: rewrite 重写原理
{% alert success no-icon %}

- AOF 文件持续增长而过大时，会 fork 出一条新进程来将文件重写（先写临时文件最后再 rename），遍历新进程的内存中数据，每条记录有一条 Set 语句
- 重写 aof 文件的操作，并没有读取旧的 aof 文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的 aof 文件，这点和快照有点类似

{% endalert %}

Redis 会记录上次重写时的 AOF 大小，默认配置是当 AOF 文件大小是上次 rewrite 后大小的一倍且文件大于 64M 时触发 rewrite 机制

{% alert info no-icon %}

当使用 AOF 方式做持久化的时候， Redis 会使用单个 write(2) 命令将事务写入到磁盘中。然而，如果 Redis 服务器因为某些原因被管理员杀死，或者遇上某种硬件故障，那么可能只有部分事务命令会被成功写入到磁盘中。如果 Redis 在重新启动时发现 AOF 文件出了这样的问题，那么它会退出，并汇报一个错误。

使用`redis-check-aof`程序可以修复这一问题：它会移除 AOF 文件中不完整事务的信息，确保服务器可以顺利启动。从 2.2 版本开始，Redis 还可以通过乐观锁（optimistic lock）实现 CAS （check-and-set）操作

{% endalert %}

#### 总结

:+1: AOF 优点
{% alert info no-icon %}

- 每修改同步：appendfsync always 同步持久化，每次发生数据变更会立即记录到磁盘性能较差但数据完整性比较好
- 每秒同步 appendfsync everysec 异步操作，每秒记录 如果一秒内宕机，只有一秒的数据丢失
- 不同步 appendfsync no 从不同步

{% endalert %}

:persevere: AOF 缺点
{% alert info no-icon %}

- 相同数据集的数据而言，aof 文件要远大于 rdb 文件，恢复速度慢于 rdb
- Aof 运行效率要慢于 rdb，每秒同步策略效率较好，不同步效率和 rdb 相同

{% endalert %}

### 持久化总结

RDB 在恢复大的数据集的时候比 AOF 快，但是 RDB 数据丢失风险大。根据 aof 所使用的 fsync 策略，aof 的速度可能会慢于 rdb，对于相同的数据集来说，aof 文件的体机通常要大于 RDB 文件的体积

## Redis 事务

`MULTI`,`EXEC`,`DISCARD`和`WATCH`是 Redis 事务相关的命令，事务可以一次执行多个命令，并且带有以下两个重要的保证：
{% alert success no-icon %}

- 事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
- 事务是一个原子操作：事务中的命令要么全部被执行，要么全部都不执行。

{% endalert %}

:sparkles: redis 中的事务和 RDB 中的事务不同，redis 中的事务将所有命令放到一个队列中，一次性、顺序性、排他性的执行一系列命令

### 常用命令

|        命令         |                                             描述                                             |
| :-----------------: | :------------------------------------------------------------------------------------------: |
|      `DISCARD`      |                             取消事务，放弃执行事务块内的所有命令                             |
|       `MULTI`       |                                     标记一个事务块的开始                                     |
|       `EXEC`        |                                    执行所有事务块内的命令                                    |
|      `UNWATCH`      |                               取消 WATCH 命令对所有 key 的监视                               |
| `WATCH key [key …]` | 监视一个(或多个) key ，如果在事务执行之前这个(或这些) key 被其他命令所改动，那么事务将被打断 |

{% alert info no-icon %}

`EXEC` 命令负责触发并执行事务中的所有命令，如果客户端在使用 `MULTI` 开启了一个事务之后，却因为断线而没有成功执行 `EXEC` ，那么事务中的所有命令都不会被执行;另一方面，如果客户端成功在开启事务之后执行 `EXEC` ，那么事务中的所有命令都会被执行

{% endalert %}

{% alert success no-icon %}

`MULTI` 命令用于开启一个事务，它总是返回 `OK` 。 `MULTI` 执行之后， 客户端可以继续向服务器发送任意多条命令， 这些命令不会立即被执行，而是被放到一个队列中， 当 `EXEC` 命令被调用时， 所有队列中的命令才会被执行。另一方面， 通过调用 `DISCARD` ， 客户端可以清空事务队列， 并放弃执行事务

{% endalert %}

```redis
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> set foo 1
QUEUED
127.0.0.1:6379> INCR foo
QUEUED
127.0.0.1:6379> EXEC
1) OK
2) (integer) 2
```

`EXEC` 命令的回复是一个数组， 数组中的每个元素都是执行事务中的命令所产生的回复。 其中， 回复元素的先后顺序和命令发送的先后顺序一致。当客户端处于事务状态时， 所有传入的命令都会返回一个内容为 `QUEUED` 的状态回复（status reply）， 这些被入队的命令将在 EXEC 命令被调用时执行。

{% alert info no-icon %}

:notes: 当事务队列中存在错误的命令时，此时如果再执行事务，将由于错误的命令导致整个事务的失败

{% endalert %}

### 事务中的错误

事务中的错误存在两种形式：**全体连坐**和**冤头债主**两种，事务在执行 EXEC 之前，入队的命令可能会出错（全体连坐）；命令可能在 EXEC 调用之后失败（冤头债主）

#### 全体连坐

{% alert success no-icon %}

事务在执行 EXEC 之前，入队的命令可能会出错

{% endalert %}

命令可能会产生语法错误（参数数量错误，参数名错误等等）或者其他更严重的错误，比如内存不足（如果服务器使用 maxmemory 设置了最大内存限制的话），类似 Java 编译异常，出现错误全部不能执行

```redis
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> set name z3
QUEUED
127.0.0.1:6379> get name
QUEUED
127.0.0.1:6379> set email
(error) ERR wrong number of arguments for 'set' command
127.0.0.1:6379> exce
(error) ERR unknown command `exce`, with args beginning with:
127.0.0.1:6379> exec
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379>

```

对于发生在 `EXEC` 执行之前的错误，客户端以前的做法是检查命令入队所得的返回值：如果命令入队时返回 `QUEUED` ，那么入队成功；否则，就是入队失败。如果有命令在入队时失败，那么大部分客户端都会停止并取消这个事务。不过，从 Redis 2.6.5 开始，服务器会对命令入队失败的情况进行记录，并在客户端调用 `EXEC` 命令时，拒绝执行并自动放弃这个事务。

#### 冤头债主

在 EXEC 命令执行之后所产生的错误，并没有对它们进行特别处理：即使事务中有某个/某些命令在执行时产生了错误，事务中的其他命令仍然会继续执行，类似于 Java 运行异常

```redis
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> set age 11
QUEUED
127.0.0.1:6379> incr age
QUEUED
127.0.0.1:6379> set email abc@163.com
QUEUED
127.0.0.1:6379> incr email
QUEUED
127.0.0.1:6379> get age
QUEUED
127.0.0.1:6379> EXEC
1) OK
2) (integer) 12
3) OK
4) (error) ERR value is not an integer or out of range
5) "12"
```

#### 为什么 redis 不支持回滚（roll back）

:question: 在冤头债主模式中，为什么当事务失败了，不进行回滚而是继续执行余下的命令，这种做法可能会感觉十分奇怪？

:+1:这么做的优点是
{% alert success no-icon %}

- Redis 命令只会因为错误的语法而失败（并且这些问题不能在入队时发现），或是命令用在了错误类型的键上面，这也就是说，从实用性的角度来说，失败的命令是由编程错误造成的，而这些错误应该在开发的过程中被发现，而不是出现在生产环境中
- 因为不需要对回滚进行支持，所以 Redis 的内部可以保持简单且快速

另一种观点认为，Redis 处理事务的做法会产生 BUG，然而需要注意的是，在通常情况下，回滚并不能解决编程错误带来的问题

{% endalert %}

举个例子， 如果你本来想通过 INCR 命令将键的值加上 1 ， 却不小心加上了 2 ，又或者对错误类型的键执行了 INCR ， 回滚是没有办法处理这些情况的。

### 放弃事务

当执行 [DISCARD 命令时， 事务会被放弃， 事务队列会被清空， 并且客户端会从事务状态中退出：

```redis
127.0.0.1:6379> SET foo 1
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> INCR foo
QUEUED
127.0.0.1:6379> DISCARD
OK
127.0.0.1:6379> get foo
"1"
```

### watch 监控

WATCH 使得 EXEC 命令需要有条件地执行：事务只能在所有被监视键都没有被修改的前提下执行，如果这个前提不能满足的话，事务就不会被执行

WATCH 命令可以为 Redis 事务提供 CAS 行为，

```redis
val = GET mykey
val = val + 1
SET mykey $val
```

上面的这个实现在只有一个客户端的时候可以执行得很好。 但是， 当多个客户端同时对同一个键进行这样的操作时， 就会产生竞争条件。举个例子， 如果客户端 A 和 B 都读取了键原来的值， 比如 10 ， 那么两个客户端都会将键的值设为 11 ， 但正确的结果应该是 12 才对。

存在 WATCH，就可以轻松地解决这类问题

```redis
WATCH mykey
val = GET mykey
val = val + 1
MULTI
SET mykey $val
EXEC
```

使用上面的代码， 如果在 [WATCH 执行之后， [EXEC] 执行之前， 有其他客户端修改了 `mykey` 的值， 那么当前客户端的事务就会失败。 程序需要做的， 就是不断重试这个操作， 直到没有发生碰撞为止。

这种形式的锁被称作乐观锁， 它是一种非常强大的锁机制。 并且因为大多数情况下， 不同的客户端会访问不同的键， 碰撞的情况一般都很少， 所以通常并不需要进行重试。

当 [EXEC] 被调用时， 不管事务是否成功执行， 对所有键的监视都会被取消

另外， 当客户端断开连接时， 该客户端对键的监视也会被取消。

使用无参数的 [UNWATCH 命令可以手动取消对所有键的监视。 对于一些需要改动多个键的事务， 有时候程序需要同时对多个键进行加锁， 然后检查这些键的当前值是否符合程序的要求。 当值达不到要求时， 就可以使用 [UNWATCH 命令来取消目前对键的监视， 中途放弃这个事务， 并等待事务的下次尝试。

### 事务执行阶段

- 开启：以 MULTI 开始一个事务
- 入队：将多个命令入队到事务中，接到这些命令并不会立即执行，而是放到等待执行的事务队列里面
- 执行：由 EXEC 命令触发事务

:sparkles:事务特性

- 单独的隔离操作：事务中所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断
- 没有隔离级别的概念：队列中的命令没有提交之前都不会实际的被执行，因为事务提交钱任何指令都不会被实际执行，也就不存在事务内的查询要看到事务里的更新，在事务外查询不能看到这个问题
- 不保证原子性：redis 同一个事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚

:notes:不遵循传统的 ACIA 中的 AI

## 消息队列

消息队列主要分为两种，分别是：生产者消费者模式和发布订阅模式，这两种模式 Redis 都支持

:notes:虽然 Redis 拥有这个功能，但是存在专门进行消息队列的中间件，所以到时候还是使用专门的消息队列中间件比较好

### 观察者模式（发布订阅模式）

进程间的一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息

下图展示了频道 channel1 ， 以及订阅这个频道的三个客户端 —— client2 、 client5 和 client1 之间的关系

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/202112292137605.png %}

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户端：

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/202112292138592.png %}

#### 常用命令

|                   命令                    |               描述               |
| :---------------------------------------: | :------------------------------: |
|      PSUBSCRIBE pattern [pattern …]       | 订阅一个或多个符合给定模式的频道 |
| PUBSUB subcommand [argument [argument …]] |      查看订阅与发布系统状态      |
|          PUBLISH channel message          |      将信息发送到指定的频道      |
|    PUNSUBSCRIBE [pattern [pattern …]]     |      退订所有给定模式的频道      |
|       SUBSCRIBE channel [channel …]       |  订阅给定的一个或多个频道的信息  |
|     UNSUBSCRIBE [channel [channel …]]     |         指退订给定的频道         |

## redis 高可用与集群

虽然 redis 可以实现单机的数据持久化，但无论是 RDB 也好或者 AOF 也好，都解决不了单点宕机问题，即一旦单台 redis 服务器本身出现系统故障、硬件故障等问题后，就会直接造成数据的丢失，因此需要使用另外的技术来解决单点问题

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/202112292139801.png %}

## 主从复制

:question:是什么

- 以防止单机部署时出现问题，整个服务就不能访问这种情况，主从复制可以做到自动将主机上的内容同步到从机中

:sparkles:能干嘛

- 读写分离
- 容灾恢复

`Master-Slaver`机制,Master 以写为主，Slave 以读为主

### 配置

:notes:配从不配主

slaver 配置命令：`slaveof 主库IP 主库端口`

每次与 master 断开后，都需要重新连接，除非配置到 redis.conf 文件（具体位置在`##### REPLICATION ####`）

可以通过如下命令查看 redis 主从复制的状态

```redis
127.0.0.1:6379> info replication
## Replication
role:master
connected_slaves:0
master_replid:81c4ffb7155436aa176fd8e71c5b35454dbc3d08
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
```

修改配置文件细节操作

- 拷贝多个 redis.conf 文件，按’redis[port].conf’重命名
- 开启 daemonize yes
- pid 文件名字
- 指定端口
- log 文件名字
- dump.rdb 名字

### 常见主从配置三招

Redis Slave 也要开启持久化并设置和 master 同样的连接密码，因为后期 slave 会有提升为 master 的可能，slave 端切换 master 后，会丢失之前的所有数据

一旦某个 slaver 成为一个 master 的 slave，redis slave 服务会清空当前 redis 服务器上的所有数据并将 master 的数据导入到自己的内存，但是断开同步关系后不会删除当前已经同步过的数据

#### 一主两从

#### 薪火相传

- 上一个 Slave 可以是下一个 slave 的 Master，Slave 同样可以接收其他 slaves 的连接和同步请求，那么该 slave 作为了链条中下一个的 master, 可以有效减轻 master 的写压力（奴隶的奴隶还是奴隶）
- 中途变更转向：会清除之前的数据，重新建立拷贝最新的

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/202112292139446.png %}

#### 反客为主

`SLAVEOF no one`

- 使当前数据库停止与其他数据库的同步，转成主数据库

### 复制原理

slave 启动成功连接到 master 后会发送一个 sync 命令

master 接收到命令，启动后台的存盘进程，同时收集所有接受到的用于修改数据集命令，在后台进程执行完毕后，master 将传送整个数据文件到 slave，以完成一次完全同步

全量复制：而 slave 服务在接收到数据库文件数据后，将其存盘并加载到内存中

增量复制：master 继续将新的所有收集到的修改命令依次传给 slave，完成哦同步

但是只要是重新连接 master，一次完全同步将被自动执行

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/202112292140980.png %}

## 哨兵模式（sentinel）

一组 sentinel 能同时监控多个 master

反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

### 使用步骤

新建`sentinel.conf`文件

配置哨兵

- sentinel monitor 被监控数据库名字（自己起名字）127.0.0.1 6379 1
- `sentinel monitor <master-group-name> <ip> <port> <quorum>`
- 最后一个数字 1，表示主机挂掉后 slave 投票看让谁接替成为主机，票数多者成为 master

启动哨兵

`redis-sentinel ./sentinel.conf`

:notes:如果之前的 master 宕机之后重新加入到 redis 集群中，原本的 master 将变成 slave

复制的缺点

复制延时，由于所有的写操作都是先在 Master 上操作，然后同步更新到 slave 上，所以从 Master 同步到 slave 机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，slave 机器数量的增加也会使这个问题更加严重

## 附录

[官网对事务的介绍](http://www.redis.cn/topics/transactions.html)
[深入理解 redis scan](https://www.jianshu.com/p/c78639d3f0ce)
