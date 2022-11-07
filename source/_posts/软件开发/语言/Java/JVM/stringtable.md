---
title: StringTable
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories:
  - Java
tags: JVM
keywords: StringTable
excerpt: 字符串是一个特殊的结构，那么在 JVM 底层中，字符串到底是如何存储的？
date: 2022-04-01 00:42:29
thumbnailImage:
---

<!-- toc -->

## String 基本特性

:sparkles: `String`类具有如下基本特性：
{% alert success no-icon %}

- String 表示字符串，使用一对`""`引起来表示
- 声明为`final`，不可被继承
- String 实现了 Serializable 接口支持序列化
- String 实现了 Comparable 接口支持比较大小
- String 在 JDK8 及以前内部定义了`final char[] value`用于存储字符串数据；JDK9 时改为`byte[]`

{% endalert %}

String：代表不可变的字符序列，简称：**不可变性**

{% alert success no-icon %}

- 当对字符串重新赋值时，需要重写指定内存区域赋值，不能使用原有的 value 进行赋值
- 当对现有的字符串进行连接操作时，也需要重新指定内存区域赋值，不能使用原有的 value 进行赋值
- 当调用 string 的 replace()方法修改指定字符或字符串时，也需要重新指定内存区域赋值，不能使用原有的 value 进行赋值

{% endalert %}

```java
public class StringTest {
   String str = new String("good");
   char[] ch = {'t', 'e', 's', 't'};

   public void change(String str, char ch[]) {
      str = "test ok";
      ch[0] = 'b';
   }

   public static void main(String[] args) {
      StringTest stringTest = new StringTest();
      stringTest.change(stringTest.str, stringTest.ch);
      System.out.println(stringTest.str);//good
      System.out.println(stringTest.ch);//best
   }
}
```

{% alert info no-icon %}

:notes: String 传参数时可以当作基本数据类型使用，对基本数据类型的包装类因此也会被当作基本数据类型使用

{% endalert %}

通过字面量的方式（区别于 new）给一个字符串赋值，此时的字符串值声明在字符串常量池中。字符串常量池保证不会存储相同内容的字符串

String 的 String Pool 是一个固定大小的 Hashtable，默认值大小长度是 1009。如果放进 String Pool 的 String 非常多，就会造成 Hash 冲突严重，从而导致链表会很长，而链表长了后直接会造成的影响就是当调用`String.intern`时性能会大幅下降，使用`-XX:StringTablesize`可设置 StringTable 的长度
{% alert success no-icon %}

- 在 jdk6 中 StringTable 是固定的，就是 1009 的长度，所以如果常量池中的字符串过多就会导致效率下降很快。StringTablesize 设置没有要求
- 在 jdk7 中，StringTable 的长度默认值是 60013，StringTablesize 设置没有要求
- 在 JDK8 中，设置 StringTable 长度的话，1009 是可以设置的最小值

{% endalert %}

## String 存储结构变更

在 JDK 9 及以后，将底层的`char[]`转变为`byte[]`

### Motivation

{% blockquote JSR官方文档  %}

The current implementation of the `String` class stores characters in a `char` array, using two bytes (sixteen bits) for each character. Data gathered from many different applications indicates that strings are a major component of heap usage and, moreover, that most `String` objects contain only Latin-1 characters. Such characters require only one byte of storage, hence half of the space in the internal `char` arrays of such `String` objects is going unused.

{% endblockquote%}

目前 String 类的实现将字符存储在一个`char`数组中，**每个字符使用两个字节**（16 位），从许多不同的应用中收集到的数据表明，**字符串是堆使用的主要组成部分**，此外，**大多数字符串对象只包含 Latin-1 字符**，这些字符**只需要一个字节的存储空间**，因此这些字符串对象的内部字符数组中**有一半空间是浪费的**

### Description

{% blockquote  JSR官方文档 %}

We propose to change the internal representation of the `String` class from a UTF-16 `char` array to a `byte` array plus an encoding-flag field. The new `String` class will store characters encoded either as ISO-8859-1/Latin-1 (one byte per character), or as UTF-16 (two bytes per character), based upon the contents of the string. The encoding flag will indicate which encoding is used.

String-related classes such as `AbstractStringBuilder`, `StringBuilder`, and `StringBuffer` will be updated to use the same representation, as will the HotSpot VM's intrinsic string operations.

This is purely an implementation change, with no changes to existing public interfaces. There are no plans to add any new public APIs or other interfaces.

The prototyping work done to date confirms the expected reduction in memory footprint, substantial reductions of GC activity, and minor performance regressions in some corner cases.

{% endblockquote%}

我们建议将 String 类的内部表示方法从`UTF-16`**字符数组**改为**字节数组加编码标志域**（编码标志将表明使用的是哪种编码）

新的 String 类将根据字符串的内容，以`ISO-8859-1/Latin-1`（每个字符一个字节）或`UTF-16`（每个字符两个字节）的方式存储字符编码。

与字符串相关的类，如`AbstractStringBuilder`、`StringBuilder`和`StringBuffer`将被更新以使用相同的表示方法，HotSpot VM 的内在字符串操作也是如此.

这纯粹是一个实现上的变化，对现有的公共接口没有变化。目前没有计划增加任何新的公共 API 或其他接口,迄今为止所做的原型设计工作证实了内存占用的预期减少，GC 活动的大幅减少，以及在某些角落情况下的轻微性能倒退

:notebook:String 不再用`char[]` 存储改成了`byte[] `加上编码标记，节约了一些空间

```java
public final class String implements java.io.Serializable,
                        Comparable<String>, CharSequence {
    @Stable
    private final byte[] value;
}
```

## String 的内存分配

在 Java 语言中有 8 种基本数据类型和一种比较特殊的类型 String。这些类型为了使它们在运行过程中速度更快、更节省内存，都提供了一种常量池的概念.常量池就类似一个 Java 系统级别提供的缓存，8 种基本数据类型的常量池都是系统协调的

:sparkles:String 类型的常量池比较特殊，它的主要使用方法有两种：
{% alert success no-icon %}

- 直接使用双引号声明的`String`对象会直接存储再常量池种
- 如果不是用双引号声明的`String`对象，可以使用`String`提供的`intern()`方法

{% endalert %}

## String table 位置

Jdk6 及以前，字符串常量池存放在永久代

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/imgjvm-stringtabl-jdk6.png)

Java 7 中 Oracle 的工程师对字符串池的逻辑做了很大的改变，即**将字符串常量池的位置调整到 Java 堆**内

{% alert success no-icon %}

- 所有的字符串都保存在堆（Heap）中，和其他普通对象一样，这样可以让你在进行调优应用时仅需要调整堆大小就可以了
- 字符串常量池概念原本使用得比较多，但是这个改动使得我们有足够的理由让我们重新考虑在 Java 7 中使用`String.intern()`

{% endalert %}

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/imgjvm-stringtable-jdk7.png)

:thinking: StringTable 位置为什么要调整

{% blockquote JSR官方文档  %}

**Synopsis:** In JDK 7, interned strings are no longer allocated in the permanent generation of the Java heap, but are instead allocated in the main part of the Java heap (known as the young and old generations), along with the other objects created by the application. This change will result in more data residing in the main Java heap, and less data in the permanent generation, and thus may require heap sizes to be adjusted. Most applications will see only relatively small differences in heap usage due to this change, but larger applications that load many classes or make heavy use of the `String.intern()` method will see more significant differences.

{% endblockquote%}

简介：在 JDK 7 中，内部字符串不再分配在 Java 堆的永久代中，而是分配在 Java 堆的主要部分，与应用程序创建的其他对象一起。这种变化将导致更多的数据驻留在主 Java 堆中，而更少的数据在永久代中，因此可能需要调整堆的大小。大多数应用程序将看到由于这一变化而导致的堆使用的相对较小的差异，**但加载许多类或大量使用 String.intern()方法的大型应用程序将看到更明显的差异**

## 字符串拼接操作

- 常量与常量的拼接结果在常量池，原理是编译期优化
- 常量池中不会存在相同内容的变量
- 只要其中有一个是变量，结果就在堆中。变量拼接的原理是 StringBuilder
- 如果拼接的结果调用`intern()`方法，则主动将常量池中还没有的字符串对象放入池中，并返回此对象地址

编译器优化的例子：

```java
public static void test1() {
   // 都是常量，前端编译期会进行代码优化
   // 通过idea直接看对应的反编译的class文件，会显示 String s1 = "abc"; 说明做了代码优化
   String s1 = "a" + "b" + "c";
   String s2 = "abc";
   // true，有上述可知，s1和s2实际上指向字符串常量池中的同一个值
   System.out.println(s1 == s2);
}
```

变量与常量间的拼接操作

```java
public static void test5() {
    String s1 = "javaEE";
    String s2 = "hadoop";

    String s3 = "javaEEhadoop";
    String s4 = "javaEE" + "hadoop";
    String s5 = s1 + "hadoop";
    String s6 = "javaEE" + s2;
    String s7 = s1 + s2;

    System.out.println(s3 == s4); // true 编译期优化
    System.out.println(s3 == s5); // false s1是变量，不能编译期优化
    System.out.println(s3 == s6); // false s2是变量，不能编译期优化
    System.out.println(s3 == s7); // false s1、s2都是变量
    System.out.println(s5 == s6); // false s5、s6 不同的对象实例
    System.out.println(s5 == s7); // false s5、s7 不同的对象实例
    System.out.println(s6 == s7); // false s6、s7 不同的对象实例

    String s8 = s6.intern();
    System.out.println(s3 == s8); // true intern之后，s8和s3一样，指向字符串常量池中的"javaEEhadoop"
}
```

- 不实用 final 修饰，即为变量，如 s3 的求解，会通过 new StringBuilder 进行拼接
- 使用`final`关键字修饰，最终变量退化为常量，会在编译器进行代码优化，在实际开发中，能够使用 final 的地方尽量使用

```java
public void test6(){
    String s0 = "beijing";
    String s1 = "bei";
    String s2 = "jing";
    String s3 = s1 + s2;
    System.out.println(s0 == s3); // false s3指向对象实例，s0指向字符串常量池中的"beijing"
    String s7 = "shanxi";
    final String s4 = "shan";
    final String s5 = "xi";
    String s6 = s4 + s5;
    System.out.println(s6 == s7); // true s4和s5是final修饰的，编译期就能确定s6的值了
}
```

在大量的实验验证下，通过 StringBuilder 的 append 方式的速度，要比直接对 String 使用`+`拼接的方式快大约 8000 倍，比 StringBuffer 的 append 方法快 4 倍左右，在实际开发中，对于需要多次或大量拼接的操作，在不考虑线程安全问题时，我们就应该尽可能使用 StringBuilder 进行 append 操作。并且如果提前知道需要拼接 String 的个数，就应该直接使用带参构造器指定 capacity，以减少扩容次数

## inter()的使用

当调用 intern 方法时，如果池子里已经包含了一个与这个 Strin·g 对象相等的字符串，正如 equals(Object)方法所确定的，那么池子里的字符串会被返回。否则，这个 String 对象被添加到池中，并返回这个 String 对象的引用

由此可见，对于任何两个字符串 s 和 t，当且仅当 s.equals(t)为真时，s.intern() == t.intern()为真，所有字面字符串和以字符串为值的常量表达式都是`interned`，返回一个与此字符串内容相同的字符串，但保证是来自一个唯一的字符串池

## G1 的 String 去重操作

```java
//需要s1、s2j
String s1 = new String("hello");
String s2 = new String("hello");
```

string 池子的操作，String s = new string(“hello world”)有几个对象生成，生成对象的 s 存放在哪里

## 附录

[new String("123") 创建了几个对象？](https://www.cnblogs.com/niceyoo/p/11100090.html)
[String s=new String("abc")创建了几个对象?](https://www.cnblogs.com/ydpvictor/archive/2012/09/09/2677260.html)
[面试题之 String str = new String("abc"); 创建了几个对象](https://blog.csdn.net/limingchuan123456789/article/details/14150327)
[官方表名更改 String 源码原因](http://openjdk.java.net/jeps/254)
[Java 函数传参(String 的不可变性)](https://blog.csdn.net/qq_32483145/article/details/81130562)
[StringTable 调整官方说明](https://www.oracle.com/java/technologies/javase/jdk7-relnotes.html#jdk7changes)
[StringBuffer 和 StringBuilder 的区别](https://blog.csdn.net/mad1989/article/details/26389541)
[String a="123"创建对象个数问题](https://blog.csdn.net/baidu_27969827/article/details/79219708)
