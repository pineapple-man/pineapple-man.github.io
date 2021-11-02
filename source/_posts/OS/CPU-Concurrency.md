---
title: CPU 与并发
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2021-10-22 19:53:14
thumbnailImage: https://gitee.com/mingchaohu/blog-image/raw/master/image/v2-e898e919d0061b49ac2fe8ac0ad190ec_720w.jpg
categories: 高并发
tags: 操作系统
keywords: 
    - CPU
    - 如何设置线程个数
excerpt: 之前一直对CPU相关的术语有些纠缠不清楚，本文将这些概念进行整理
---
<!-- toc -->
## CPU 相关术语

:thinking:程序都运行在 CPU 之上，那么CPU的核心数和线程数之间是什么关系尼？答案隐藏在本文中

### 物理 CPU 数（Physical CPU）

![中央处理器](https://gitee.com/mingchaohu/blog-image/raw/master/image/1200px-Intel_Core_I7-920_Boxed_-_14.JPG)

:sparkles:Physical CPU指的是：**主板上实际插入的 CPU 硬件个数**

:persevere:但是这一概念经常被泛泛的说成是 cpu 数，这很容易导致与 core 数，processor 数等概念混淆

:notes:由于在主板上引入多个 CPU 插槽（socket）需要更复杂的硬件支持（连接不同插槽的CPU到内存和其他资源），通常只会在服务器才会插多个物理CPU，家用PC机的主板上只会有一个CPU插槽

### 核心（Core）

最初，每个物理CPU上只有一个核心（single core），此时对于操作系统而言，同一时刻只能运行一个进程/线程；随着**摩尔定律的生效**，CPU厂商开始在单个物理 cpu 上增加核心（实实在在的硬件存在），此时**单核心物理CPU**变成了**双核心 cpu（dual-core cpu）**以及**多核心 cpu（multiple cores）**，如此一来，操作系统就能在同一时刻运行多个进程/线程

> 一个双核心 cpu 就是操作系统同一时刻能够运行两个进程/线程的物理设备，下图是多核心 CPU 的示意图

![CPU muliti croe ](https://gitee.com/mingchaohu/blog-image/raw/master/image/v2-e898e919d0061b49ac2fe8ac0ad190ec_720w.jpg)

### 同时多线程技术（simultaneous multithreading）和超线程技术（hyper–threading/HT）

:dart:同时多线程技术和超线程技术的本意是：**提高单个CPU核心同一时刻能够执行的多线程数**，充分利用单个CPU Core 的计算能力

> simultaneous multithreading 缩写 SMT，是 AMD 和其他 CPU 厂商的称呼
>
> hyper–threading 是 Intel 的称呼，可以认为 hyper–threading 是 SMT 的一种具体技术实现
>
> :sparkles:在类似的多线程技术下，产生了如下等价的术语：
>
> - 虚拟核心：virtual core
> - 逻辑处理器：logical processor
> - 线程：thread

### 查看系统CPU资源

#### Linux 系统

```bash
#查看物理 cpu 数
cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

## 查看每个物理 cpu 中 核心数(core 数)
cat /proc/cpuinfo | grep "cpu cores" | uniq

## 查看总的逻辑 cpu 数（processor 数）
cat /proc/cpuinfo| grep "processor"| wc -l

## 查看 cpu 型号
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c

## 同时查看上述信息
lscpu
```

#### Windows 系统

![Windows系统查看CPU信息](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20211017124212223.png)

## CPU 的并发

CPU本身并没有线程和进程的概念，它只会**按照机器指令的要求执行某个动作**，**线程以及进程等概念是从操作系统角度看待任务产生的两个概念**，因此宏观角度看待**CPU不存在并发性抽象，并发的抽象发生在操作系统层级**

:notes:CPU 的核心数和线程个数有什么必然的关系吗？（没有任何关系）

- 单个核心可以跑任意多个线程，只要计算机内存足够就可以；
- 计算机可以有多核但只运行单线程

:notebook:CPU 与并发的总结

- CPU根本不理解自己执行的指令属于哪个线程，CPU也不需要理解这些，**CPU需要做的事情就是根据PC寄存器中的地址从内存中取出响应的指令，然后执行就足够了**
- 计算机中应该存在多少线程，线程之间如何运行都是**操作系统**应该操心的事情，CPU 根本不用考虑这些问题，它只需要执行任务就可以了

## 操作系统的并发

:sparkles:操作系统的并发来自于操作系统的多任务特点
:thinking:什么是操作系统的多任务？

- 多任务就是在操作系统的管理下（通过**修改CPU的PC寄存器**），能够在一段时间内执行多个任务（多个线程）
- 多任务是并行/并发的雏形

### 进程与线程

:vs:CPU 与 OS 如何看待进程和线程

- CPU不知道执行的某一段机器指令属于A任务还是B任务，其内部没有进程和线程的抽象
- 操作系统知道机器指令属于哪个任务，同时操作系统还能知道任务A和B任务是否属于同一个**地址空间**

> 如果两个任务属于同一个地址空间，那么任务A和任务B就是我们熟悉的**多线程**；如果不属于同一个地址空间，那么任务A和任务B就是我们熟悉的**多进程**

### 单核与多线程

假设现在有两个任务，任务A和任务B，每个任务需要的计算时间都是5分钟，那么无论是任务A和任务B串行执行还是放到两个线程中并行执行，在单核环境下执行完这两个任务总需要10分钟

:notes:由于核心数是固定的，再怎么并发，单核心上再某一时刻，只能执行一个任务

:older_man:**线程概念为程序设计人员提供了一种变成抽象**，可以将一项任务进行划分，然后把每一个子任务放到一个个线程中运行

:book:单核多线程的案例（阻塞式I/O）

- 如果没有多线程，执行阻塞式I/O时整个进程会被操作系统暂停，但如果开启两个线程，其中一个线程被阻塞时另一个线程依然可以继续向前推进，就可以充分利用系统资源

## 总结

:notes:CPU 自身对于并发还是并行是不敏感的，这些都是我们在操作系统层面讨论的东西，CPU 唯一限制开发人员的就是：**CPU 所具有的物理性质（核心数目）**，就是再优秀的并发算法，在单核CPU上，表现的性能比串行性能差

:thinking:为了让多线程程序**更好的利用 cpu 资源**，应当如何做到 CPU 与线程数的权衡尼？

- 对于计算密集型程序的建议是：`(logical processor count ) + 1`，有的建议是`(logical processor count)*1.5`，但是大部分都是经验之谈，需要根据实际业务场景进行测试才能得到适合自己的答案

:question:那么如何**了解机器拥有的 logical processor 数尼**？

- `cpuinfo`中查看：`cat /proc/cpuinfo | grep "processor" | wc -l`
- `top`命令查看：`cpu0,cpu1,...`
- 编程：比如在 Java 中用 `Runtime.getRuntime().availableProcessors()`

## 附录

[cpu 核心数与线程数——何健超](https://zhuanlan.zhihu.com/p/86855590)

[中央处理器——wiki](https://zh.wikipedia.org/wiki/%E4%B8%AD%E5%A4%AE%E5%A4%84%E7%90%86%E5%99%A8)

[CPU 核数与线程数有什么关系？](https://mp.weixin.qq.com/s?__biz=Mzg4OTYzODM4Mw==&amp;mid=2247485900&amp;idx=1&amp;sn=36e943eb6fe9baaf9cce64b2295136c8&amp;chksm=cfe9954cf89e1c5a83073f70e832d953e487781dbfe9c7cb55c5db241c80791bfc570e27daa8&token=985761181&lang=zh_CN#rd)
