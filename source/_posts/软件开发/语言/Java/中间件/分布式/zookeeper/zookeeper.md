---
title: ZooKeeper 环境配置
date: 2021-10-19 19:53:14
tags: [分布式组件, ZooKeeper]
toc: true
metaAlignment: center
categories: 中间件
excerpt: ZooKeeper作为常用的分布式组件，本文主要记录ZooKeeper的环境配置（单机和集群）
---

<!-- toc -->

ZooKeeper 单机安装分为两种方式：**压缩包安装**和**Docker 安装**本文都会介绍；另一方面，ZK 集群同样有不同的配置方式，本文主要记录如何通过 Docker 创建 ZooKeeper 集群

:dart:本文将以`zk-3.5.7`版本为基础，介绍不同方式的安装步骤

## 源码安装单机 ZK

### 资源准备

:book:前往[官网](https://zookeeper.apache.org/)进行下载

![image-20211012000505154](/assets/images/zookeeper/image-20211012000505154.png)

![image-20211012000511505](/assets/images/zookeeper/image-20211012000511505.png)

![image-20211012000516730](/assets/images/zookeeper/image-20211012000516730.png)

### 安装

:notes:由于`ZooKeeper`是由 Java 编写的，所以**需要基础的 Java 环境**

```bash
# 在下载 ZooKeeper 文件的目录下解压文件
tar -zxvf apache-zookeeper-3.5.7-bin.tar.gz
# 进入到解压目录
cd apache-zookeeper-3.5.7-bin
# ZooKeeper 简单配置，配置文件名必须为zoo.cfg，否则启动将出错
cp conf/zoo_sample.cfg conf/zoo.cfg
```

### 配置

```bash
# 通信心跳时间， Zookeeper服务器与客户端心跳时间，单位毫秒
tickTime=2000

# Leader 与 Follower初始连接时能容忍的最多心跳数（tickTime的数量）
# 超过则通信失败
initLimit=10

# Leader 与 Foloower 同步通信时限如果超过 syncLimit * tickTime
# Leader 认为 Follwer 死掉，从服务器列表中删除 Follwer
syncLimit=5

# 保存 ZooKeeper 中的数据，默认是tmp目录，生产环境一定要更改
dataDir=/root/zk/zkDataDir	#这里更改为一个持久化目录

# 客户端连接端口，通常不做修改
clientPort=2181
```

:notes:必须修改`dataDir`配置，因为这是持久化数据保存的目录，不能为`/tmp`（`/tmp`目录会被 Linux 系统定时清理）；如果配置的目录不存在，zookeeper 会自动创建

### 简单的操作

```bash
# 启动 zookeeper
[root@localhost zookeeper-3.5.7]$ bin/zkServer.sh start
# 查看进程是否启动
[root@localhost zookeeper-3.5.7]$ jps
60000 QuorumPeerMain
60060 Jps
# 查看状态
[root@localhost zookeeper-3.5.7]$ bin/zkServer.sh status
/usr/bin/java
ZooKeeper JMX enabled by default
Using config: /root/zk/zookeeper-3.5.7/bin/../conf/zoo.cfg
# 启动客户端连接服务端
[root@localhost zookeeper-3.5.7]$ bin/zkCli.sh
# 退出客户端
[zk: localhost:2181(CONNECTED) 0] quit
# 停止 Zookeeper
[root@localhost zookeeper-3.5.7]$ bin/zkServer.sh s
```

## 容器单机安装 ZK

容器的方式非常简单，只要有`docker`环境就可以了

```bash
# 拉取最新的 zooKeeper 镜像
docker pull zookeeper

# 部署 zk，由于需要做存储卷绑定，所以需要开启--privileged，否则将会出错
docker run --privileged --network host -v /data/zookeeper_data/data:/data -v /data/zookeeper_data/conf:/conf  --name zk-2181 zookeeper
```

## 创建 ZK 集群

:notes:血的教训，一定**要先关闭防火墙**，否则总是出现稀奇古怪的问题

| 主机名 |     IP 地址     |
| :----: | :-------------: |
| Master | 192.168.117.128 |
| node1  | 192.168.117.129 |
| node2  | 192.168.117.130 |

:notes:三台服务器能够相互之间通过主机名`ping`通，对集群中的每一台主机进行如下操作：

```bash
mkdir /data/zookeeper/data
mkdir /data/zookeeper/conf
cd /data/zookeeper/data
touch myid
```

### 创建机器编号

集群中不同主机，对应的机器编号是不同的，分配的机器编号如下

|  主机  | zk 服务器编号 |
| :----: | :-----------: |
| master |       0       |
| node1  |       1       |
| node2  |       2       |

```bash
# 修改每个主机的 zk 服务器编号文件，例如 master 主机的编号修改过程如下
echo "0" >> /data/zookeeper/data/myid
```

其余主机类似的操作，仅仅是服务器编号不同

### 修改配置文件

将集群中每一台主机的配置文件`zoo.cfg`修改如下:

```bash
tickTime=2000

initLimit=10

syncLimit=5

dataDir=/data

dataLogDir=/data/log

clientPort=2181

# 在 zoo.cfg 下面增加以下配置：Server.A=B:C:D的形式
server.0=192.168.117.128:2888:3888
server.1=192.168.117.129:2888:3888
server.2=192.168.117.130:2888:3888
```

- A：标识服务器编号

- B：标识服务器地址

- C：标识服务器 Follower 与集群中的 Leader 服务器交换信息的端口

- D：标识再选举通信端口，如果 Leader 服务器挂了，这个端口就是用来执行选举时服务器相互通信的端口，通过这个端口进行重新选举 leader

### 启动容器

```bash
docker run -d --rm --privileged --network host -v /data/zookeeper_data/data:/data -v /data/zookeeper_data/conf:/conf -p 2888:2888 -p 3888:3888  --name zk zookeeper
```

### 查看 zk 集群状态

完成以上步骤，现在检查 zk 集群是否真的搭建完成

```bash
# 即可进入 zk 容器中
docker exec -it zk /bin/bash
# 进入容器后，输入以下命令查看zk集群状态
./bin/zkServer.sh status

# 输入以下信息表示 zk 集群创建完成
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Client port found: 2181. Client address: localhost. Client SSL: false.
Mode: follower
```
