---
title: Java BIO
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: Java
tags: Java IO
keywords: BIO
excerpt: 本文主要讲解 Java 中的传统 IO
date: 2022-04-09 23:01:46
---

<!-- toc -->

## 概述

Java BIO（Java Blocking IO）是**传统的 Java IO 编程**，其相关的类和接口在`java.io`中，下面将介绍两种常见的 IO 模型分类方式

### 同步与异步

描述的是用户线程与内核的交互方式，与消息的通知机制有关：
{% alert success no-icon %}

同步：当一个同步调用发出后，需要等待返回消息（用户线程不断去询问），才能继续进行；
异步：当一个异步调用发出后，调用者不能立即得到返回消息但是可以去执行其他任务，当调用完成后会通过状态、通知和回调等机制来通知调用者，调用完成

{% endalert %}
简而言之，同步和异步两者之间的关系如下：
{% alert success no-icon %}

同步：同步等待消息通知，消息返回才能继续进行
异步：异步等待消息通知，完成后被调系统通过回调等机制来通知调用者

{% endalert %}

### 阻塞与非阻塞

阻塞和非阻塞指的是得到结果之前，当前线程能否继续执行下去，具体的细节如下：
{% alert success no-icon %}

阻塞：发起请求后，当前线程会被挂起，知道请求结果返回为止才能继续执行业务逻辑

非阻塞：发起请求后，在得到结果之前，当前线程并不会被挂起，而是通过不断询问的方式，查看当前请求是否完成

{% endalert %}

## BIO 通信方式简介

以前大多数网络通信方式都是阻塞模式的，即:
{% alert warning no-icon %}

- 客户端向服务器端发出请求后，客户端会一直等待(不会再做其他事情)，直到服务器端返回结果或者网络出现问题
- 服务器端同样的，当在处理某个客户端 A 发来的请求时，另一个客户端 B 发来的请求会等待，直到服务器端的这个处理线程完成上一个处理

{% endalert %}

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java/io/java-io-bio-1.png)

:persevere: 传统 BIO 问题
{% alert success no-icon %}

- 同一时间，服务器只能接受来自于客户端 A 的请求信息；虽然客户端 A 和客户端 B 的请求是同时进行的，但客户端 B 发送的请求信息只能等到服务器接受完 A 的请求数据后，才能被接受

- 由于服务器一次只能处理一个客户端请求，当处理完成并返回后(或者异常时)，才能进行第二次请求的处理。很显然，这样的处理方式在高并发的情况下，是不能采用的

{% endalert %}

## BIO 模式下接收多个客户端

常规方式下，一个服务端只能接收一个客户端的通信请求，**如果服务端需要处理很多个客户端的消息通信请求，可以使用多线程进行处理**。

:sparkles:客户端每发起一个请求，服务端就创建一个新的线程来处理这个客户端的请求，这样就实现了**一个客户端一个线程模型**

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java-io-bio-overview.png %}

:persevere: 使用多线程方式处理 BIO 存在的弊端
{% alert success no-icon %}

1. 每个 Socket 接收到，都会创建一个线程，线程的竞争、切换上下文影响性能；
2. 每个线程都会占用栈空间和 CPU 资源
3. 并不是每个 socket 都进行 IO 操作
4. 客户端的并发访问增加时。服务端将呈现 1:1 的线程开销，访问量越大，系统将发生线程栈溢出，线程创建失败，最终导致进程宕机或者僵死，从而不能对外提供服务

{% endalert %}

## 多线程方式——伪异步方式

在上述案例中：客户端的并发访问增加时。服务端将呈现 1:1 的线程开销，访问量越大，系统将发生线程栈溢出，线程创建失败，最终导致进程宕机或者僵死，从而不能对外提供服务。

​ 接下来我们采用一个伪异步 I/O 的通信框架，采用线程池和任务队列实现，当客户端接入时，将客户端的 Socket 封装成一个 Task(该任务实现 java.lang.Runnable 线程任务接口)交给后端的线程池中进行处理。JDK 的线程池维护一个消息队列和 N 个活跃的线程，对消息队列中 Socket 任务进行处理，由于线程池可以设置消息队列的大小和最大线程数，因此，它的资源占用是可控的，无论多少个客户端并发访问，都不会导致资源的耗尽和宕机。如下图：

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java/io/java-io-multithread-fakeasync.png)

:sparkles:采用伪异步方式特点

- 伪异步 io 采用了线程池实现，因此避免了为每个请求创建一个独立线程造成线程资源耗尽的问题，但由于底层依然是采用的同步阻塞模型，因此无法从根本上解决问题
- 如果单个消息处理的缓慢，或者服务器线程池中的全部线程都被阻塞，那么后续 socket 的 i/o 消息都将在队列中排队。新的 Socket 请求将被拒绝，客户端会发生大量连接超时

## 实现软件组播

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java/io/java-io-groupcast.png)

## 深入分析

BIO 的问题关键不在于是否使用了多线程(包括线程池)处理这次请求，而在于 accept()、read()的操作点都是被阻塞

:thinking: 为什么`accept()`、`read()`方法会被阻塞？
{% alert success no-icon %}

- 服务器线程发起一个 accept 动作，**询问操作系统** 是否有新的 socket 套接字信息从端口 X 发送过来

- socket 套接字的 IO 模式支持是基于操作系统的，那么自然**同步 IO/异步 IO 的支持就是需要操作系统级别的了**

- 如果操作系统没有发现有套接字从指定的端口 X 来，那么操作系统就会等待。这样`serverSocket.accept()`方法就会一直等待。

{% endalert %}

## 总结

BIO 模型最大的缺陷是：缺乏扩展性，不能处理高性能、高并发场景，线程是 JVM 中非常宝贵的资源，当线程数膨胀后，系统的性能就会急剧下降，随着并发访问量的继续增大，系统就会出现堆栈溢创建新线程失败等问题，导致 Server 不能对外提供服务。

## 附录

[BIO 详解](https://www.pdai.tech/md/java/io/java-io-bio.html)
