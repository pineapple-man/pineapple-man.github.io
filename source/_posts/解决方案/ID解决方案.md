---
title: 分布式 ID 生成系统解决方案
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 解决方案
tags: 分布式组件
keywords:
  - 分布式 id 生成器
excerpt: 本文主要总结目前常用的分布式ID生成器的解决方案
date: 2021-11-11 23:09:00
thumbnailImage:
---

<!-- toc -->

## 概述

:question:什么是全局分布式 ID？

{% alert success no-icon%}

在业务数据量不大的时候，单库单表完全可以支撑现有业务（这个时候想怎么实现就怎么实现）；当数据进一步增大，MySQL 就需要进行**主从同步读写分离**；随着数据日渐增长，主从同步也扛不住了，就需要对数据库进行**分库分表**，但分库分表后需要有一个`唯一ID`来标识一条数据，数据库的自增 ID 显然不能满足需求（容易产生 ID 冲突），如：订单、优惠券需要有唯一 ID 做标识。此时一个能够生成`全局唯一ID`的系统是非常必要的，这个**全局唯一 ID 生成器**就叫**分布式 ID 生成系统**

{%endalert%}

:question:为什么需要用全局分布式 ID？
{% blockquote 美团技术团队 https://tech.meituan.com/2017/04/21/mt-leaf.html Leaf——美团点评分布式ID生成系统%}

在复杂分布式系统中，往往需要对大量的数据和消息进行唯一标识。如在美团点评的金融、支付、餐饮、酒店、猫眼电影等产品的系统中，数据日渐增长，对数据分库分表后需要有一个唯一 ID 来标识一条数据或消息，数据库的自增 ID 显然不能满足需求；如订单、骑手、优惠券也都需要有唯一 ID 做标识。

{% endblockquote%}

:question:全局分布式 ID 要完成哪些功能？
{% alert success no-icon%}

- 全局唯一性：不能出现重复的 ID 号，既然是唯一标识，这是最基本的要求

- 趋势递增：在 MySQL InnoDB 引擎中使用的是聚集索引，由于多数 RDBMS 使用 B-tree 的数据结构来存储索引数据，在主键的选择上面我们应该尽量使用有序的主键保证写入性能

- 单调递增：保证下一个 ID 一定大于上一个 ID，例如事务版本号、IM 增量消息、排序等特殊需求

- 信息安全：如果 ID 是连续的，恶意用户的扒取工作就非常容易做了，直接按照顺序下载指定 URL 即可；如果是订单号就更危险了，所以在一些应用场景下，会需要 ID 无规则、不规则

- 高可用性：除了对 ID 号码自身的要求，业务还对 ID 号生成系统的可用性要求极高，如果 ID 生成系统瘫痪，故障可能广播到整个系统

- 分片支持：可以控制 ShardingId。比如某一个用户的文章要放在同一个分片内，这样查询效率高，修改也容易

{%endalert%}

:notebook:一个 ID 生成系统应该做到以下几点：平均延迟和 TP999 延迟都要尽可能低；可用性 5 个 9；高 QPS

## 分布式 ID 生成方式

:sparkles:常见的存在以下九种分布式 ID 生成方式：

{% image fancybox fig-100  center  https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/分布式ID/分布式id_0.jpg%}

### UUID

UUID(Universally Unique Identifier)的标准型式包含 32 个 16 进制数字，以连字号分为五段，形式为`8-4-4-4-12`的 36 个字符，示例：`550e8400-e29b-41d4-a716-446655440000`，到目前为止业界一共有 5 种方式生成 UUID，详情见 IETF 发布的 UUID 规范[A Universally Unique IDentifier (UUID) URN Namespace](https://www.ietf.org/rfc/rfc4122.txt)

```java
public static void main(String[] args) {
       String uuid = UUID.randomUUID().toString().replaceAll("-","");
       System.out.println(uuid);
 }
```

:vs:优缺点分析
{% alert success no-icon%}
:+1:性能非常高：本地生成，没有网络消耗
:persevere:缺点比较多

- 不易于存储：UUID 太长，16 字节 128 位，通常以 36 长度的字符串表示，很多场景不适用
- 信息不安全：基于 MAC 地址生成 UUID 的算法可能会造成 MAC 地址泄露，这个漏洞曾被用于寻找梅丽莎病毒的制作者位置
- UUID 是无序的。不是单调递增的，而现阶段主流的数据库主键索引都是选用的 B+树索引，对于无序长度过长的主键插入效率比较低，传输数据量大，不可读
- UUID 往往是使用字符串存储，查询的效率比较低。
  {%endalert%}

{% blockquote MySQL官方对主键建议  %}
All indexes other than the clustered index are known as secondary indexes. In InnoDB, each record in a secondary index contains the primary key columns for the row, as well as the columns specified for the secondary index. InnoDB uses this primary key value to search for the row in the clustered index.**_If the primary key is long, the secondary indexes use more space, so it is advantageous to have a short primary key_**

{% endblockquote%}

:notebook:UUID 的生成非常简单，但是并不适用于实际的业务需求。用作订单号这样的字符串没有丝毫的意义，看不出和订单相关的有用信息；对于数据库查询也非常不利，并不是非常推荐使用 UUID 作为分布式 ID 生成器

### 基于数据库自增 ID

基于数据库的`auto_increment`自增 ID 完全可以充当**分布式 ID**。具体实现：需要一个单独的 MySQL 实例用来生成 ID，建表结构如下：

```sql
CREATE DATABASE `SEQ_ID`;
CREATE TABLE SEQID.SEQUENCE_ID (
    id bigint(20) unsigned NOT NULL auto_increment,
    value char(10) NOT NULL default '',
    PRIMARY KEY (id),
) ENGINE=MyISAM;
insert into SEQUENCE_ID(value)  VALUES ('values');
```

:vs:优缺点分析
{% alert success no-icon%}
:+1:非常简单，利用现有数据库系统的功能实现，成本小，有 DBA 专业维护;ID 号单调自增，可以实现一些对 ID 有特殊要求的业务
:persevere:强依赖 DB，当 DB 异常时整个系统不可用，属于致命问题。配置主从复制可以尽可能的增加可用性，但是数据一致性在特殊情况下难以保证。主从切换时的不一致可能会导致重复发号;ID 发号性能瓶颈限制在单台 MySQL 的读写性能
{%endalert%}
:notebook:当我们需要一个 ID 的时候，向表中插入一条记录返回主键 ID，但这种方式有一个比较致命的缺点，访问量激增时 MySQL 本身就是系统的瓶颈，用它来实现分布式服务风险比较大，不推荐！
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/分布式ID/分布式id_3.png %}

### 基于数据库集群模式

单点数据库方式不可取，对上边的方式做一些高可用优化，换成主从模式集群。害怕一个主节点挂掉没法用，那就做双主模式集群，也就是两个 Mysql 实例都能单独的生产自增 ID。那这样还会有个问题，两个 MySQL 实例的自增 ID 都从 1 开始，会生成重复的 ID，可以使用如下方式解决：

```sql
TicketServer1:
auto-increment-increment = 2
auto-increment-offset = 1

TicketServer2:
auto-increment-increment = 2
auto-increment-offset = 2
```

假设我们要部署 N 台机器，步长需设置为 N，每台的初始值依次为 0,1,2…N-1 那么整个架构就变成了如下图所示：

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/分布式ID/分布式id_2.png %}

:question:如果集群后的性能还是扛不住高并发咋办？
{% alert success no-icon%}

进行 MySQL 扩容增加节点，这是一个比较麻烦的事。从上图可以看出，水平扩展的数据库集群，有利于解决数据库单点压力的问题，同时为了 ID 生成特性，将自增步长按照机器数量来设置。

增加第三台`MySQL`实例需要人工修改一、二两台`MySQL实例`的起始值和步长，把`第三台机器的ID`起始生成位置设定在比现有`最大自增ID`的位置远一些，但必须在一、二两台`MySQL实例`ID 还没有增长到`第三台MySQL实例`的`起始ID`值，否则`自增ID`就要出现重复了，**必要时可能还需要停机修改**
{%endalert%}

:vs:优缺点分析
{% alert success no-icon%}
:+1:解决 DB 单点故障问题
:persevere:这种方案弊端仍然非常明显：

- 系统水平扩展比较困难
- ID 没有了单调递增的特性，只能趋势递增，这个缺点对于一般业务需求不是很重要，可以容忍
- 数据库压力还是很大，每次获取 ID 都得读写一次数据库，只能靠堆机器来提高性能
  {%endalert%}

### 基于数据库的号段模式

号段模式是当下分布式 ID 生成器的主流实现方式之一，号段模式可以理解为从数据库批量的获取自增 ID，每次从数据库取出一个号段范围，例如 (1,1000] 代表 1000 个 ID，具体的业务服务将本号段，生成 1~1000 的自增 ID 并加载到内存。表结构如下：

```mysql
CREATE TABLE id_generator (
  id int(10) NOT NULL,
  max_id bigint(20) NOT NULL COMMENT '当前最大id',
  step int(20) NOT NULL COMMENT '号段的布长',
  biz_type	int(20) NOT NULL COMMENT '业务类型',
  version int(20) NOT NULL COMMENT '版本号',
  PRIMARY KEY (`id`)
)
```

|   字段   |                           含义                           |
| :------: | :------------------------------------------------------: |
| biz_type |                     代表不同业务类型                     |
|  max_id  |                    当前最大的可用 id                     |
|   step   |                      代表号段的长度                      |
| version  | 是一个乐观锁，每次都更新 version，保证并发时数据的正确性 |

例如：生成的 ID 在数据库表中存储为如下信息：

| id  | biz_type | max_id | step | version |
| --- | -------- | ------ | ---- | ------- |
| 1   | 101      | 1000   | 2000 | 0       |

等这批号段 ID 用完，再次向数据库申请新号段，对`max_id`字段做一次`update`操作，`update max_id= max_id + step`，update 成功则说明新号段获取成功，新的号段范围是`(max_id ,max_id +step]`、

```mysql
update id_generator set max_id = #{max_id+step}, version = version + 1 where version = # {version} and biz_type = XXX
```

由于多业务端可能同时操作，所以采用版本号`version`乐观锁方式更新，这种`分布式ID`生成方式不强依赖于数据库，不会频繁的访问数据库，对数据库的压力小很多

### 基于 Redis 模式

`Redis`也同样可以实现，原理就是利用`redis`的 `incr`命令实现 ID 的原子性自增

```redis
127.0.0.1:6379> set seq_id 1     // 初始化自增ID为1
OK
127.0.0.1:6379> incr seq_id      // 增加1，并返回递增后的数值
(integer) 2
```

:sparkles:用`redis`实现需要注意一点，要考虑到 redis 持久化的问题。`redis`有两种持久化方式`RDB`和`AOF`

{% alert success no-icon%}

- `RDB`会定时将一个快照进行持久化，假如连续自增但`redis`没及时持久化，而这会 Redis 挂掉了，重启 Redis 后会出现 ID 重复的情况

- `AOF`会对每条写命令进行持久化，即使`Redis`挂掉了也不会出现 ID 重复的情况，但由于 incr 命令的特殊性，会导致`Redis`重启恢复的数据时间过长
  {%endalert%}

### 雪花算法

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/分布式ID/分布式id_4.png %}
雪花算法（Snowflake）是 twitter 公司内部分布式项目采用的 ID 生成算法，最终生成的是占 8 个字节 Long 类型的 ID
{% alert success no-icon%}
Snowflake ID 组成结构：`正数位`（占 1 比特）+ `时间戳`（占 41 比特）+ `机器ID`（占 5 比特）+ `数据中心`（占 5 比特）+ `自增值`（占 12 比特），总共 64 比特组成的一个 Long 类型

- 第一个 bit 位（1bit）：Java 中 long 的最高位是符号位代表正负，正数是 0，负数是 1，一般生成 ID 都为正数，所以默认为 0

- 时间戳部分（41bit）：毫秒级的时间，不建议存当前时间戳，而是用（当前时间戳 - 固定开始时间戳）的差值，可以使产生的 ID 从更小的值开始；41 位的时间戳可以使用 69 年，(1L << 41) / (1000L _ 60 _ 60 _ 24 _ 365) = 69 年

- 工作机器 id（10bit）：也被叫做`workId`，这个可以灵活配置，机房或者机器号组合都可以。

- 序列号部分（12bit），自增值支持同一毫秒内同一个节点可以生成 4096 个 ID
  {%endalert%}

### 百度（uid-generator）

`uid-generator`是基于`Snowflake`算法实现的，与原始的 snowflake 算法不同在于，uid-generator 支持自**定义时间戳**、**工作机器 ID**和**序列号**等各部分的位数，而且`uid-generator`中采用用户自定义`workId`的生成策略

### 美团（Leaf）

`Leaf`同时支持号段模式和`snowflake`算法模式，可以切换使用

#### 号段模式

```mysql
DROP TABLE IF EXISTS `leaf_alloc`;

CREATE TABLE `leaf_alloc` (
  `biz_tag` varchar(128)  NOT NULL DEFAULT '' COMMENT '业务key',
  `max_id` bigint(20) NOT NULL DEFAULT '1' COMMENT '当前已经分配了的最大id',
  `step` int(11) NOT NULL COMMENT '初始步长，也是动态调整的最小步长',
  `description` varchar(256)  DEFAULT NULL COMMENT '业务key的描述',
  `update_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '数据库维护的更新时间',
  PRIMARY KEY (`biz_tag`)
) ENGINE=InnoDB;

```

然后在项目中开启`号段模式`，配置对应的数据库信息，并关闭`snowflake`模式，字段模式大致架构如下：
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/分布式ID/分布式id_1.png %}

#### snowflake 模式

`Leaf`的 snowflake 模式依赖于`ZooKeeper`，与原始 snowflake 算法在`workId`的生成上不同。`Leaf`中`workId`是基于`ZooKeeper`的顺序 Id 来生成的，每个应用在使用`Leaf-snowflake`时，启动时都会都在`Zookeeper`中生成一个顺序 Id，相当于一台机器对应一个顺序节点，也就是一个`workId`

### 滴滴（Tinyid）

`Tinyid`是基于号段模式原理实现的与`Leaf`如出一辙，这里不给出详细使用方式

## 附录

[Leaf——美团点评分布式 ID 生成系统](https://tech.meituan.com/2017/04/21/mt-leaf.html)
[一口气说出 9 种分布式 ID 生成方式，面试官有点懵了](https://juejin.cn/post/6844904065747402759)
