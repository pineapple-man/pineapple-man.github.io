---
title: Happens-Before 原则
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: Java
keywords: Happens-Before
excerpt: 本文主要讲解 Java 高并发中的一个重要知识点—— Happens-Before 规则
date: 2022-04-01 15:25:10
thumbnailImage:
---

<!-- toc -->

> 以下所有内容都摘抄自《深入理解 Java 虚拟机》

## 概述

Java 中的 「 先行发生 」原则，能够简单的判断数据是否存在竞争，线程是否安全的非常有用的是手段。依赖这个原则，我们可以通过几条简单的规则解决并发环境下两个操作之间是否可能存在冲突的所有问题，而不需要陷入 Java 内存模型复杂的定义中

## 先行发生原则

下面是 Java 内存模型下一下 「 天然的 」先行发生关系，这些先行发生关系无须任何同步器协助就已经存在，可以在编码中直接使用。如果两个操作之间的关系不在此列，并且无法从下列规则推导出来，则它们就没有顺序性保障，虚拟机可以对它们随意地进行冲排序

### 程序次序规则（Program Order Rule）

在一个线程内，按照控制流顺序，书写在前面的操作先行发生于书写在后面的操作。注意，这里说的控制流顺序而不是程序代码顺序，因为要考虑分支、循环等结构

### 管程锁定规则（Monitor Lock Rule）

一个 unlock 操作先行发生于后面对同一个锁的 lock 操作。这里必须是「 同一个锁 」，并且「 后面 」 指时间上的先后

### volatile 变量规则（Volatile Variable Rule）

对一个 volatile 变量的写操作先行发生于后面对这个变量的读操作，这里 「 后面 」同样是指时间上的先后

### 线程启动规则（Thread Start Rule）

Thread 对象的 start（） 方法先行发生于此线程的每一个动作

### 线程终止规则（Thread Termination Rule）

线程中的所有操作都先行发生于对此线程的终止检测，我们可以通过`Thread::join()`方法是否结束、`Thread::isAlive()`对返回值等手段检测线程是否已经终止执行

### 线程中断规则（Thread Interruption Rule)

对线程 `interrupt()` 方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过`Thread::interrupted()`方法检测到是否有中断发生

### 对象终结规则

一个对象的初始化完成（构造函数执行结束）先行发生于它的 finalize() 方法的开始

### 传递性（Transitivity）

如果操作 A 先行发生于操作 B，操作 B 先行发生于操作 C，那就可以得到操作 A 先行发生于操作 C 的结论
