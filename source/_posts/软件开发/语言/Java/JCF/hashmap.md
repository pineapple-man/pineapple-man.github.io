---
title: HashMap 从入门到入土
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: Java
keywords: HashMap
excerpt: 本文主要分析面试中常见的集合类——HashMap
date: 2022-04-20 18:19:22
thumbnailImage:
---
<!-- toc -->

## 概述

:sparkles:HashMap特点

- HashMap基于哈希表的Map接口实现，是以key-value存储形式存在，即主要用来存放键值对
- HashMap的实现不是同步的，这意味着它不是线程安全的
- HashMap的key、value都可以为null
- HashMap中的映射不是有序的

:notebook:HashMap的变更

- JDK1.8 之前 HashMap 由 数组+链表 组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突**(两个对象调用的hashCode方法计算的哈希码值一致导致计算的数组索引值相同)**而存在的（**拉链法**解决冲突）
- JDK1.8 以后在解决哈希冲突时有了较大的变化，**当链表长度大于阈值（或者红黑树的边界值，默认为 8）并且当前数组的长度大于64时，此时此索引位置上的所有数据改为使用红黑树存储**

:notes:将链表转换成红黑树前会判断，即时阈值大于8，但是数组长度小于64，此时并不会将链表变为红黑树，而是选择进行数组扩容

## JDK7

之所以把*HashSet*和*HashMap*放在一起讲解，是因为二者在Java里有着相同的实现，前者仅仅是对后者做了一层包装，也就是说*HashSet*里面有一个*HashMap*(适配器模式)。因此本文将重点分析*HashMap*

```java
// HashSet 构造函数
public HashSet() {
    map = new HashMap<>();
}
```

*HashMap*实现了*Map*接口，即允许放入`key`为`null`的元素，也允许插入`value`为`null`的元素；除该类未实现同步外，其余跟`Hashtable`大致相同；跟*TreeMap*不同，该容器不保证元素顺序，根据需要该容器可能会对元素重新哈希，元素的顺序也会被重新打散，因此不同时间迭代同一个*HashMap*的顺序可能会不同。 根据对冲突的处理方式不同，哈希表有两种实现方式，一种开放地址方式(Open addressing)，另一种是冲突链表方式(Separate chaining with linked lists)。

:sparkles:**Java7 HashMap 采用的是冲突链表方式**

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/img/java/jcf/jcf-HashMap.png)

从上图容易看出，如果选择合适的哈希函数，`put()`和`get()`方法可以在常数时间内完成。但在对*HashMap*进行迭代时，需要遍历整个table以及后面跟的冲突链表。因此对于迭代比较频繁的场景，不宜将*HashMap*的初始大小设的过大。

有两个参数可以影响*HashMap*的性能: 初始容量(inital capacity)和负载系数(load factor)。初始容量指定了初始`table`的大小，负载系数用来指定自动扩容的临界值。当`entry`的数量超过`capacity*load_factor`时，容器将自动扩容并重新哈希。对于插入元素较多的场景，将初始容量设大可以减少重新哈希的次数。

将对象放入到*HashMap*或*HashSet*中时，有两个方法需要特别关心: `hashCode()`和`equals()`。**`hashCode()`方法决定了对象会被放到哪个`bucket`里，当多个对象的哈希值冲突时，`equals()`方法决定了这些对象是否是“同一个对象”**。所以，如果要将自定义的对象放入到`HashMap`或`HashSet`中，需要*@Override*`hashCode()`和`equals()`方法。

#### `get()`











## JDK 8

Java8 对 HashMap 进行了一些修改，最大的不同就是利用了红黑树，所以其由 **数组+链表+红黑树** 组成



### 附录
[HashTable，HashMap和ConcurrentHashMap的区别？](https://blog.csdn.net/weixin_43718267/article/details/89419899)
[大厂之路一由浅入深、并行基础、源码分析一 “J.U.C”之collections框架：ConcurrentHashMap扩容迁移等方法的源码分析（学妹哭着对我说：太难了！！！！我要回家！！）](https://blog.csdn.net/wwj17647590781/article/details/118151008?spm=1001.2014.3001.5501)
[ConcurrentHashMap(JDK1.8)为什么要放弃Segment](https://blog.csdn.net/mian_csdn/article/details/70185104)
[HashMap默认加载因子为什么选择0.75？](https://www.cnblogs.com/aspirant/p/11470928.html)
[HashMap与ConcurrentHashMap的区别](https://www.cnblogs.com/signheart/p/21d463eebb54f3e9139da3d43ee7bfda.html)
[HashMap多线程死循环问题](https://blog.csdn.net/xuefeng0707/article/details/40797085)
[常见的hash算法及其原理](https://blog.csdn.net/Beyond_2016/article/details/81286360)