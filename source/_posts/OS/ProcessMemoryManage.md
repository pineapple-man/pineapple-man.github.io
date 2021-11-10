---
title: 进程内存管理(1)
date: 2021-10-21 23:47:15
toc:  true
clearReading: true
thumbnailImagePosition: bottom
metaAlignment: center
categories: 操作系统
tags: 操作系统
keywords: 
    - RSS
    - VSS
    - PSS
    - USS
    - 进程内存管理
excerpt: 本文将简单介绍操作系统中评估进程内存占用的四个指标
---

<!--toc-->

## 缘起

最近实验室要求制作一个python程序监控的工具类，在开发的过程中遇到了一些曾经在操作系统中没有学习到的知识在这里进行记录

> :notes:本文大部分内容总结自网络，难免有上下文不连贯的地方，望谅解！

:sparkles:结论先行，后面会有详细介绍

|  术语   |                          含义                          |
| :-----: | :----------------------------------------------------: |
| **VSS** |    Virtual Set Size 进程可以访问的所有内存空间大小     |
| **RSS** |         Resident Set Size 进程实际使用物理内存         |
| **PSS** | Proportional Set Size 进程实际使用的物理内存(更加准确) |
| **USS** |         Unique Set Size 进程独自占用的物理内存         |

:notes:通常而言，以上四个指标有如下规律：`VSS >= RSS >= PSS >= USS`

## VSS-Virtual Set Size

VSS也称为VSZ，表示一个进程的**虚拟内存占用情况**，包含**共享库占用的全部内存**，以及**分配但未使用内存**，还包括了**可能不在RAM中的内存**，比如一个进程Swap出去的内存

> VSZ is the Virtual Memory Size. It **includes all memory that the process can access**, including memory that is swapped out, memory that is allocated, but not used, and memory that is from shared libraries

:notes:VSS 很少被用于判断一个进程真实内存使用量

{% image fancybox  fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/d3a92df3efa0df779418bed820e6dcd31f6cbbc6.png   %}


## RSS-Resident Set Size

RSS称为常驻内存大小，表示一个进程**实际使用的物理内存大小**，以及包含**共享库占用的全部内存**

> RSS is the Resident Set Size and is used to show how much memory is allocated to that process and is in RAM. It does not include memory that is swapped out. It does include memory from shared libraries as long as the pages from those libraries are actually in memory. It does include all stack and heap memory.

:notes:使用RSS指标还是可能会造成误导，因为它仅仅表示**该进程所使用的所有共享库的大小**，它不管有多少个进程使用该共享库，该共享库仅被加载到内存一次。所以，**RSS并不能准确反映单进程的内存占用情况**

{% image fancybox  fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/d10c3c6e80e70309ce73bfa874d92d56606fa989.png  %}

> :sparkles:这里有一个例子可以更好的加深对以上两个指标的理解：
>
> 如果一个进程，程序的大小有 500K，链接的共享库大小有 2500K，堆栈内存共有 200K，其中 100K 进入了交换分区，进程实际加载了共享库中的 1000K 的内容，以及自己程序中的 400K 的内容，相应的 RSS 和 VSS 计算方式如下：

```
RSS:400K(当前程序使用的大小) + 1000K(实际使用共享库的大小) + 100K(实际使用堆栈大小)= 1500K
VSS:500K(程序本身大小) + 2500K(共享库本身大小) + 200K(占用堆栈内存本身大小) = 3200K
```

## PSS-Proportional Set Size

通过RSS，可以发现对于一个进程占用内存的实际情况还是**不准确**的，因为**一个共享库实际可以被很多进程共享**，如果将所有进程的RSS指标加起来，必然是会超过计算机本身内存大小的

:dart:为了弥补 RSS 指标的不正常地方，就存在了 PSS 指标

:sparkles:PSS 我将其翻译为比例内存大小，表示一个进程实际使用的物理内存，不过其中共享库的大小是按照进程数等比计算的

> 例如：有三个进程都使用了一个共享库，共占用了30页内存。那么PSS将认为每个进程分别占用该共享库10页的大小。 

:notebook:PSS是非常有用的数据，因为系统中所有进程的PSS相加的话，就刚好反映了系统中的 总共占用的内存。 而当一个进程被销毁之后， 其占用的共享库那部分比例的PSS，将会再次按比例分配给余下使用该库的进程

:persevere:PSS可能会造成一点的误导，因为当一个进程被销毁后， PSS不能准确地表示返回给全局系统的内存

{% image fancybox  fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/ee8a35925f0aafb160c58b18eb4aee3bf4762398.png%}

## USS-Unique Set Size

通常一个进程我们**并不关心共享库的使用情况**，**仅仅关注于进程本身的内存占用情况**，这时候就需要使用 USS 指标

:sparkles:USS 也称为**进程独占内存大小**，不包含共享库占用的内存。

:notebook:USS是非常有用的数据，它反映了运行一个特定进程真实的边际成本（增量成本）。**当一个进程被销毁后，USS是真实返回给系统的内存，当进程中存在一个可疑的内存泄露时，USS是最佳观察数据**

{% image fancybox  fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/255c513123c88d85d5e1137be66d0672368b8931.png %}



## 总结

{% image fancybox  fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/c5xbq6wsov.png %}

## 附录

[VSS,RSS,PSS,USS](https://www.iteye.com/blog/myeyeofjava-1837860)

[psutil](https://psutil.readthedocs.io/en/stable/#psutil.Process.memory_info)

[What is RSS and VSZ in Linux memory management](https://stackoverflow.com/questions/7880784/what-is-rss-and-vsz-in-linux-memory-management)

[Linux内存管理 一个进程究竟占用多少空间？](https://cloud.tencent.com/developer/article/1683708)

