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

:question:什么是全局分布式ID？

{% alert success no-icon%}

在业务数据量不大的时候，单库单表完全可以支撑现有业务（这个时候想怎么实现就怎么实现）；当数据进一步增大，MySQL就需要进行**主从同步读写分离**；随着数据日渐增长，主从同步也扛不住了，就需要对数据库进行**分库分表**，但分库分表后需要有一个`唯一ID`来标识一条数据，数据库的自增ID显然不能满足需求（容易产生ID冲突），如：订单、优惠券需要有唯一ID做标识。此时一个能够生成`全局唯一ID`的系统是非常必要的，这个**全局唯一ID生成器**就叫**分布式ID生成系统**

{%endalert%}

:question:为什么需要用全局分布式ID？
{% blockquote 美团技术团队 https://tech.meituan.com/2017/04/21/mt-leaf.html Leaf——美团点评分布式ID生成系统%}

在复杂分布式系统中，往往需要对大量的数据和消息进行唯一标识。如在美团点评的金融、支付、餐饮、酒店、猫眼电影等产品的系统中，数据日渐增长，对数据分库分表后需要有一个唯一ID来标识一条数据或消息，数据库的自增ID显然不能满足需求；如订单、骑手、优惠券也都需要有唯一ID做标识。

{% endblockquote%}

:question:全局分布式ID要完成哪些功能？
{% alert success no-icon%}
- 全局唯一性：不能出现重复的ID号，既然是唯一标识，这是最基本的要求

- 趋势递增：在MySQL InnoDB引擎中使用的是聚集索引，由于多数RDBMS使用B-tree的数据结构来存储索引数据，在主键的选择上面我们应该尽量使用有序的主键保证写入性能

- 单调递增：保证下一个ID一定大于上一个ID，例如事务版本号、IM增量消息、排序等特殊需求

- 信息安全：如果ID是连续的，恶意用户的扒取工作就非常容易做了，直接按照顺序下载指定URL即可；如果是订单号就更危险了，所以在一些应用场景下，会需要ID无规则、不规则

- 高可用性：除了对ID号码自身的要求，业务还对ID号生成系统的可用性要求极高，如果ID生成系统瘫痪，故障可能广播到整个系统

- 分片支持：可以控制ShardingId。比如某一个用户的文章要放在同一个分片内，这样查询效率高，修改也容易

{%endalert%}

:notebook:一个ID生成系统应该做到以下几点：平均延迟和TP999延迟都要尽可能低；可用性5个9；高QPS

## 分布式ID生成方式

:sparkles:常见的存在以下九种分布式ID生成方式：

{% image fancybox fig-100  center  https://gitee.com/mingchaohu/blog-image/raw/master/image/分布式ID.jpg%}

### UUID
UUID(Universally Unique Identifier)的标准型式包含32个16进制数字，以连字号分为五段，形式为`8-4-4-4-12`的36个字符，示例：`550e8400-e29b-41d4-a716-446655440000`，到目前为止业界一共有5种方式生成UUID，详情见IETF发布的UUID规范[A Universally Unique IDentifier (UUID) URN Namespace](https://www.ietf.org/rfc/rfc4122.txt)

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
- 不易于存储：UUID太长，16字节128位，通常以36长度的字符串表示，很多场景不适用
- 信息不安全：基于MAC地址生成UUID的算法可能会造成MAC地址泄露，这个漏洞曾被用于寻找梅丽莎病毒的制作者位置
- UUID是无序的。不是单调递增的，而现阶段主流的数据库主键索引都是选用的B+树索引，对于无序长度过长的主键插入效率比较低，传输数据量大，不可读
- UUID往往是使用字符串存储，查询的效率比较低。
{%endalert%}

{% blockquote MySQL官方对主键建议  %}
All indexes other than the clustered index are known as secondary indexes. In InnoDB, each record in a secondary index contains the primary key columns for the row, as well as the columns specified for the secondary index. InnoDB uses this primary key value to search for the row in the clustered index.***If the primary key is long, the secondary indexes use more space, so it is advantageous to have a short primary key***

{% endblockquote%}

:notebook:UUID的生成非常简单，但是并不适用于实际的业务需求。用作订单号这样的字符串没有丝毫的意义，看不出和订单相关的有用信息；对于数据库查询也非常不利，并不是非常推荐使用UUID作为分布式ID生成器

### 基于数据库自增ID
基于数据库的`auto_increment`自增ID完全可以充当**分布式ID**。具体实现：需要一个单独的MySQL实例用来生成ID，建表结构如下：
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
:+1:非常简单，利用现有数据库系统的功能实现，成本小，有DBA专业维护;ID号单调自增，可以实现一些对ID有特殊要求的业务
:persevere:强依赖DB，当DB异常时整个系统不可用，属于致命问题。配置主从复制可以尽可能的增加可用性，但是数据一致性在特殊情况下难以保证。主从切换时的不一致可能会导致重复发号;ID发号性能瓶颈限制在单台MySQL的读写性能
{%endalert%}
:notebook:当我们需要一个ID的时候，向表中插入一条记录返回主键ID，但这种方式有一个比较致命的缺点，访问量激增时MySQL本身就是系统的瓶颈，用它来实现分布式服务风险比较大，不推荐！
{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/8a4de8e8.png %}
### 基于数据库集群模式
单点数据库方式不可取，对上边的方式做一些高可用优化，换成主从模式集群。害怕一个主节点挂掉没法用，那就做双主模式集群，也就是两个Mysql实例都能单独的生产自增ID。那这样还会有个问题，两个MySQL实例的自增ID都从1开始，会生成重复的ID，可以使用如下方式解决：
```sql
TicketServer1:
auto-increment-increment = 2
auto-increment-offset = 1

TicketServer2:
auto-increment-increment = 2
auto-increment-offset = 2
```
假设我们要部署N台机器，步长需设置为N，每台的初始值依次为0,1,2…N-1那么整个架构就变成了如下图所示：

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/6d2c9ec8.png %}

:question:如果集群后的性能还是扛不住高并发咋办？
{% alert success no-icon%}

进行MySQL扩容增加节点，这是一个比较麻烦的事。从上图可以看出，水平扩展的数据库集群，有利于解决数据库单点压力的问题，同时为了ID生成特性，将自增步长按照机器数量来设置。

增加第三台`MySQL`实例需要人工修改一、二两台`MySQL实例`的起始值和步长，把`第三台机器的ID`起始生成位置设定在比现有`最大自增ID`的位置远一些，但必须在一、二两台`MySQL实例`ID还没有增长到`第三台MySQL实例`的`起始ID`值，否则`自增ID`就要出现重复了，**必要时可能还需要停机修改**
{%endalert%}

:vs:优缺点分析
{% alert success no-icon%}
:+1:解决DB单点故障问题
:persevere:这种方案弊端仍然非常明显：
- 系统水平扩展比较困难
- ID没有了单调递增的特性，只能趋势递增，这个缺点对于一般业务需求不是很重要，可以容忍
- 数据库压力还是很大，每次获取ID都得读写一次数据库，只能靠堆机器来提高性能
{%endalert%}


### 基于数据库的号段模式

号段模式是当下分布式ID生成器的主流实现方式之一，号段模式可以理解为从数据库批量的获取自增ID，每次从数据库取出一个号段范围，例如 (1,1000] 代表1000个ID，具体的业务服务将本号段，生成1~1000的自增ID并加载到内存。表结构如下：

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

|   字段   |                          含义                           |
| :------: | :-----------------------------------------------------: |
| biz_type |                    代表不同业务类型                     |
|  max_id  |                    当前最大的可用id                     |
|   step   |                     代表号段的长度                      |
| version  | 是一个乐观锁，每次都更新version，保证并发时数据的正确性 |

例如：生成的ID在数据库表中存储为如下信息：

| id   | biz_type | max_id | step | version |
| ---- | -------- | ------ | ---- | ------- |
| 1    | 101      | 1000   | 2000 | 0       |

等这批号段ID用完，再次向数据库申请新号段，对`max_id`字段做一次`update`操作，`update max_id= max_id + step`，update成功则说明新号段获取成功，新的号段范围是`(max_id ,max_id +step]`、

```mysql
update id_generator set max_id = #{max_id+step}, version = version + 1 where version = # {version} and biz_type = XXX
```

由于多业务端可能同时操作，所以采用版本号`version`乐观锁方式更新，这种`分布式ID`生成方式不强依赖于数据库，不会频繁的访问数据库，对数据库的压力小很多

### 基于Redis模式

`Redis`也同样可以实现，原理就是利用`redis`的 `incr`命令实现ID的原子性自增

```redis
127.0.0.1:6379> set seq_id 1     // 初始化自增ID为1
OK
127.0.0.1:6379> incr seq_id      // 增加1，并返回递增后的数值
(integer) 2
```

:sparkles:用`redis`实现需要注意一点，要考虑到redis持久化的问题。`redis`有两种持久化方式`RDB`和`AOF`

{% alert success no-icon%}

- `RDB`会定时将一个快照进行持久化，假如连续自增但`redis`没及时持久化，而这会Redis挂掉了，重启Redis后会出现ID重复的情况

- `AOF`会对每条写命令进行持久化，即使`Redis`挂掉了也不会出现ID重复的情况，但由于incr命令的特殊性，会导致`Redis`重启恢复的数据时间过长
{%endalert%}

### 雪花算法
{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/snowflake.png %}
雪花算法（Snowflake）是twitter公司内部分布式项目采用的ID生成算法，最终生成的是占8个字节Long类型的ID
{% alert success no-icon%}
Snowflake ID组成结构：`正数位`（占1比特）+ `时间戳`（占41比特）+ `机器ID`（占5比特）+ `数据中心`（占5比特）+ `自增值`（占12比特），总共64比特组成的一个Long类型

- 第一个bit位（1bit）：Java中long的最高位是符号位代表正负，正数是0，负数是1，一般生成ID都为正数，所以默认为0

- 时间戳部分（41bit）：毫秒级的时间，不建议存当前时间戳，而是用（当前时间戳 - 固定开始时间戳）的差值，可以使产生的ID从更小的值开始；41位的时间戳可以使用69年，(1L << 41) / (1000L * 60 * 60 * 24 * 365) = 69年

- 工作机器id（10bit）：也被叫做`workId`，这个可以灵活配置，机房或者机器号组合都可以。

- 序列号部分（12bit），自增值支持同一毫秒内同一个节点可以生成4096个ID
{%endalert%}

### 百度（uid-generator）

`uid-generator`是基于`Snowflake`算法实现的，与原始的 snowflake 算法不同在于，uid-generator 支持自**定义时间戳**、**工作机器ID**和**序列号**等各部分的位数，而且`uid-generator`中采用用户自定义`workId`的生成策略
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
{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/5e4ff128.png %}

#### snowflake模式

`Leaf`的snowflake模式依赖于`ZooKeeper`，与原始snowflake算法在`workId`的生成上不同。`Leaf`中`workId`是基于`ZooKeeper`的顺序Id来生成的，每个应用在使用`Leaf-snowflake`时，启动时都会都在`Zookeeper`中生成一个顺序Id，相当于一台机器对应一个顺序节点，也就是一个`workId`
### 滴滴（Tinyid）

`Tinyid`是基于号段模式原理实现的与`Leaf`如出一辙，这里不给出详细使用方式

## 附录

[Leaf——美团点评分布式ID生成系统](https://tech.meituan.com/2017/04/21/mt-leaf.html)

[一口气说出9种分布式ID生成方式，面试官有点懵了](https://juejin.cn/post/6844904065747402759)