---
title: Redis 踩坑指北
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-01-01 21:37:04
thumbnailImage:
categories: 数据库
tags: Redis
keywords: Redis 踩坑
excerpt: 本文主要记录平时使用 Redis 时遇到的所有问题
---
<!-- toc -->
## 安装错误

```c
In file included from server.c:31:0:
server.c:4999:59: error: ‘struct redisServer’ has no member named ‘cluster’
             (server.cluster_enabled && nodeIsMaster(server.cluster->myself)));
                                                           ^
cluster.h:58:27: note: in definition of macro ‘nodeIsMaster’
 #define nodeIsMaster(n) ((n)->flags & CLUSTER_NODE_MASTER)
                           ^
server.c: In function ‘main’:
server.c:5047:11: error: ‘struct redisServer’ has no member named ‘sentinel_mode’
     server.sentinel_mode = checkForSentinelMode(argc,argv);
           ^
server.c:5064:15: error: ‘struct redisServer’ has no member named ‘sentinel_mode’
     if (server.sentinel_mode) {
               ^
server.c:5131:19: error: ‘struct redisServer’ has no member named ‘sentinel_mode’
         if (server.sentinel_mode && configfile && *configfile == '-') {
                   ^
server.c:5153:168: error: ‘struct redisServer’ has no member named ‘sentinel_mode’
         serverLog(LL_WARNING, "Warning: no config file specified, using the default config. In order to specify a config file use %s /path/to/%s.conf", argv[0], server.sentinel_mode ? "sentinel" : "redis");
                                                                                                                                                                        ^
server.c:5158:11: error: ‘struct redisServer’ has no member named ‘supervised’
     server.supervised = redisIsSupervised(server.supervised_mode);
           ^
server.c:5158:49: error: ‘struct redisServer’ has no member named ‘supervised_mode’
     server.supervised = redisIsSupervised(server.supervised_mode);
                                                 ^
server.c:5159:28: error: ‘struct redisServer’ has no member named ‘daemonize’
     int background = server.daemonize && !server.supervised;
                            ^
server.c:5159:49: error: ‘struct redisServer’ has no member named ‘supervised’
     int background = server.daemonize && !server.supervised;
                                                 ^
server.c:5163:29: error: ‘struct redisServer’ has no member named ‘pidfile’
     if (background || server.pidfile) createPidFile();
                             ^
server.c:5168:16: error: ‘struct redisServer’ has no member named ‘sentinel_mode’
     if (!server.sentinel_mode) {
                ^
server.c:5178:19: error: ‘struct redisServer’ has no member named ‘cluster_enabled’
         if (server.cluster_enabled) {
                   ^
server.c:5186:19: error: ‘struct redisServer’ has no member named ‘ipfd_count’
         if (server.ipfd_count > 0 || server.tlsfd_count > 0)
                   ^
server.c:5186:44: error: ‘struct redisServer’ has no member named ‘tlsfd_count’
         if (server.ipfd_count > 0 || server.tlsfd_count > 0)
                                            ^
server.c:5188:19: error: ‘struct redisServer’ has no member named ‘sofd’
         if (server.sofd > 0)
                   ^
server.c:5189:94: error: ‘struct redisServer’ has no member named ‘unixsocket’
             serverLog(LL_NOTICE,"The server is now ready to accept connections at %s", server.unixsocket);
                                                                                              ^
server.c:5190:19: error: ‘struct redisServer’ has no member named ‘supervised_mode’
         if (server.supervised_mode == SUPERVISED_SYSTEMD) {
                   ^
server.c:5191:24: error: ‘struct redisServer’ has no member named ‘masterhost’
             if (!server.masterhost) {
                        ^
server.c:5201:19: error: ‘struct redisServer’ has no member named ‘supervised_mode’
         if (server.supervised_mode == SUPERVISED_SYSTEMD) {
                   ^
server.c:5208:15: error: ‘struct redisServer’ has no member named ‘maxmemory’
     if (server.maxmemory > 0 && server.maxmemory < 1024*1024) {
               ^
server.c:5208:39: error: ‘struct redisServer’ has no member named ‘maxmemory’
     if (server.maxmemory > 0 && server.maxmemory < 1024*1024) {
                                       ^
server.c:5209:176: error: ‘struct redisServer’ has no member named ‘maxmemory’
         serverLog(LL_WARNING,"WARNING: You specified a maxmemory value that is less than 1MB (current value is %llu bytes). Are you sure this is what you really want?", server.maxmemory);
                                                                                                                                                                                ^
server.c:5212:31: error: ‘struct redisServer’ has no member named ‘server_cpulist’
     redisSetCpuAffinity(server.server_cpulist);
                               ^
server.c: In function ‘hasActiveChildProcess’:
server.c:1480:1: warning: control reaches end of non-void function [-Wreturn-type]
 }
 ^
server.c: In function ‘allPersistenceDisabled’:
server.c:1486:1: warning: control reaches end of non-void function [-Wreturn-type]
 }
 ^
server.c: In function ‘writeCommandsDeniedByDiskError’:
server.c:3826:1: warning: control reaches end of non-void function [-Wreturn-type]
 }
 ^
server.c: In function ‘iAmMaster’:
server.c:5000:1: warning: control reaches end of non-void function [-Wreturn-type]
 }
 ^
make[1]: *** [server.o] Error 1
make[1]: Leaving directory `/opt/redis-6.0.6/src'
make: *** [all] Error 2
```


### 原因

自从`Redis 6.0`之后，编译 redis 需要支持 C11 特性，C11 特性在4.9中被引入，Centos7 默认gcc版本为 4.8.5，所以需要升级 gcc 版本

### 解决方法

```bash
yum -y install gcc gcc-c++ make tcl centos-release-scl devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
scl enable devtoolset-9 bash
```

## 主从问题

切入点问题，slave1、slave2 是从头开始复制还是从切入点开始复制?
{% alert success no-icon %}

slave 从头开始复制，会将 master 所有的内容都拷贝到 slaver 中
{% endalert %} 


从机是否可以写？set可否？

{% alert success no-icon%}
从机不可写，不可set，主机可写
{%endalert%}

主机shutdown后情况如何？从机是上位还是原地待命
{% alert success no-icon%}
从机还是原地待命（咸鱼翻身，还是咸鱼）
{%endalert%}

主机又回来了后，主机新增记录，从机还能否顺利复制？
{% alert success no-icon %}
能
{% endalert %}

其中一台从机down后情况如何？依照原有它能跟上大部队吗？
{% alert success no-icon %}
不能跟上，每次与master断开之后，都需要重新连接，除非配置进 redis.conf 文件（具体位置：redis.conf搜寻`##### REPLICATION ####`）
{% endalert %}


## 附录

[解决安装错误](https://blog.csdn.net/u011552171/article/details/108189641)

