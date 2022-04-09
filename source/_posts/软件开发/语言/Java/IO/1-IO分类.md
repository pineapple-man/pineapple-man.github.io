---
title: Java IO(一) IO 总览
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-01-20 23:12:35
categories: Java
tags: Java IO
keywords: Java IO
excerpt: Java 类库的设计者通过创建大量大类解决不同方式 I/O 通信的难题。具有讽刺意义的是，Java I/O 设计的初衷是为了避免过多的类，但是通过不停的迭代，如今的 I/O 系统提供了非常多类，让使用者感到不到所措。
---
<!-- toc -->

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java-io-overview.jpg %}
## 概述

{% alert success no-icon%}
Java 的 I/O 大概可以分成以下几类:

- 磁盘操作: File
- 字节操作: InputStream 和 OutputStream
- 字符操作: Reader 和 Writer
- 对象操作: Serializable
- 网络操作: Socket
- 新 I/O 操作: NIO
  {%endalert%}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java-io-catagory.png %}

:question:Java IO 能用来干什么？
{% alert success no-icon %}

Java 对数据的操作是通过流的方式进行的，I/O 用来处理设备之间的数据传输(**上传文件**和**下载文件**)

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java-io-stream.png %}

:notes: 输入、输出的参照物是**存储数据的介质**，把**对象读入到介质中**定义为输入，对应的，**从介质中向外读数据**就是输出

## 根据传输的数据看待 IO

从数据传输方式或者说是运输方式角度看，可以将 IO 类分为:
{% alert success no-icon %}

- 字节流（Byte 是计算机操作数据的最小单位，由 8 位 bit 组成）
- 字符流（Char 是用户可读取的最小单位，在 Java 中由 16 bit 组成）

{% endalert %}

:notes: 可以简单的认为：`字节`是给计算机看的，`字符`是给人看的

### 字节流

{% alert success no-icon %}

字节流直接操作byte类型数据，主要操作类是`OutputStream`、`InputStream`的各种子类，特点是：**不用缓冲区**，直接对文件本身操作

{% endalert %}

常用的各种子类如下图：
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java-io-stream-category.png %}

### 字符流

字符流与字节流不同，主要操作字符类型数据，主要操作类是 `Reader`、`Writer`的各种子类；特点是：**使用缓冲区缓冲字符**，不关闭流就不会输出任何内容

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java-io-character-category.png)

### 字节流和字符流的区别

:vs: 字节流与字符流对比：
{% alert success no-icon %}

- 字节流每次读取单个字节，字符流每次读取单个字符(一个字符根据编码的不同，对应的字节也不同，如 UTF-8 编码是 3 个字节，中文编码是 2 个字节)
- 字节流用来处理二进制文件(图片、MP3、视频文件)，字符流用来处理文本文件(可以看做是特殊的二进制文件只不是使用了某种编码方式，更便于人类阅读)

{% endalert %}

:question: 字节流和字符流应当如何选择？
{% alert success no-icon %}

- 如果数据所在的文件可以通过记事本打开阅读，就可以使用字符流进行操作，否则只能使用字节流
- 常用的音频必须使用字节流而不能使用字符流

{% endalert %}

:notes: 如果打开一个文件能够识别选用字符流，如果是一堆**乱码选用字节流**

### 互相转换

从以上分类角度观察，整个 IO 包分为字节流和字符流，为了将两种流联系起来，实际上，除了以上两种流之外，还存在一组的**转换类**，便于在两种流之间方便转换

{% alert success no-icon %}

- `OutputStreamWriter`：是 Writer 的子类，将输出的字符流变为字节流，即将一个字符流的输出对象变为字节流输出对象。
- `InputStreamReader`：是 Reader 的子类，将输入的字节流变为字符流，即将一个字节流的输入对象变为字符流的输入对象。

{% endalert %}

:sparkles: Java 使用双字节编码 UTF-16be，这不是指 Java 只支持这一种编码方式，而是说 char 这种类型使用 UTF-16be 进行编码。char 类型占 16 位，也就是两个字节，Java 使用这种双字节编码是为了让一个中文或者一个英文都能使用一个 char 来存储，Java 中的 IO 形式如下：

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java-io-transformer.png)

:notes: Java I/O 流的特点是：每个基类的实现类都是**以父类名作为类名的后缀**进行命名的

## 从数据操作方式看待IO

从数据来源或者操作对象角度看，IO 类可以分为：

![img](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java-io-operator-category.png)

## Java I/O 演进之路

上述的介绍只能说是对 Java IO API 有了一个全貌的了解，下面要介绍的内容将是 Java IO 系统中的核心也是一个语言的难点所在，Java 中的 IO 模型。

:notes: 注意上下文，特别强调这里表述的是<font style="color:red;font-weight:bold">Java IO</font>,而不是 Unix IO 或其他的 IO 形式。其实，Java 的 IO 模型本质上还是<font style="color:red;font-weight:bold">利用操作系统提供的接口来实现</font>,所以想要了解 Java Io 模型之前最好先了解 Linux 底层 IO 模型

### I/O 模型基础

:thinking:什么是 I/O 模型？
{% alert success no-icon %}

- I/O 模型是有上下文关系的，没有明确的上下文是不能确定所表达的 I/O 模型
- 常说的阻塞、非阻塞、同步、异步其实都是网络IO场景下的Unix I/O 模型区分
- 可以简单的将 IO 模型理解为：使用 IO 处理数据的一种套路
{% endalert %}


### Java I/O 模型
{% alert success no-icon %}

Java 共支持 3 种网络编程的/IO 模型：**BIO、NIO、AIO**，在实际通信需求下，要根据不同的业务场景和性能需求决定选择不同的 I/O 模型

{% endalert %}

#### Java BIO

在JDK 1.4 之前，基于Java的所有Socket通信都采用同步阻塞模式(BIO)，这种`请求一应答`的通信模型简化了上层的应用开发，但是在性能和可靠性方面却存在着巨大的瓶颈。当并发量增大，响应时间延迟增大之后，采用Java BIO开发的服务端只有通过硬件的不断扩容来满足高并发和低延迟，它极大的增加了企业的成本，随着集群规模的不断膨胀，系统的可维护性也面临巨大的挑战

{% alert success no-icon %}

同步阻塞(传统阻塞型)模型，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销 

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java-io-bio-overview.png %}

经典的网络服务设计如上图所示，对每个请求都会产生一个新的线程来进行处理，这种设计的缺点就是：线程创建本身也是系统资源的一次比较耗时的开销，如果并发请求达到一定数量，响应将会变慢，甚至有可能因为系统资源不足而造成系统的崩溃。

传统的 BIO 代码类似如下：
```java
public class BlokingIoServer implements Runnable{

    @Override
    public void run() {
        try {
            //将服务绑定到指定端口
            ServerSocket ss = new ServerSocket(8888);
            while (!Thread.interrupted()){
                //对 accept()方法的调 用将被阻塞，直到一个连接建立
                final Socket clientSocket = ss.accept();

                //为每个请求创建一个线程来处理
                new Thread(new Handler(clientSocket)).start();
            }
            // or, single-threaded, or a thread pool
        } catch (IOException ex) { /* ... */ }
    }
    static class Handler implements Runnable {
        final Socket socket;
        Handler(Socket s){
            socket = s;
        }
        @Override
        public void run() {
            try {
                byte[] input = new byte[1024];
                socket.getInputStream().read(input);
                byte[] output = process(input);
                socket.getOutputStream().write(output);
            } catch (IOException ex) { /* ... */ }
        }
        private byte[] process(byte[] cmd){
            //do something
            return null;
        }
    }
}
```

#### Java NIO
{% alert success no-icon %}

同步非阻塞，服务器实现模式为一个线程处理多个请求(连接)，即客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有 I/O 请求就进行处理

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java-io-nio-overview.png %}

#### Java AIO

{% alert success no-icon %}

异步非阻塞，服务器实现模式为一个有效请求一个线程，客户端的 I/O 请求都是由 OS 先完成了再通知服务器应用去启动线程进行处理，一般适用于连接数较多且连接时间较长的应用

{% endalert %}

### Java 各种IO使用场景分析

| Java IO 方式 |                           适用场景                           |
| :----------: | :----------------------------------------------------------: |
|   **BIO**    | 连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择 |
|   **NIO**    | 连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，弹幕系统，服务器间通讯等，JDK1.4 开始支持 |
|   **AIO**    | 连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用 OS 参与并发操作，JDK7 开始支持 |

:thinking: 为什么需要进阶IO？
{% alert warning no-icon %}

基础的 IO 仅仅关注于本地文件读写等操作，其实从更广义的角度来讲，任意两台计算机间的通信也是一种 IO ；进程间数据通信也是一种 IO ，因此当完成基础的 IO 学习之后，需要了解到更加复杂的 IO 模式，有效的使用 IO 能够明显的提高代码的效率

{% endalert %}


## 附录

[Java IO模型](https://zhuanlan.zhihu.com/p/75716224)
[谈一谈Java IO 模型](https://matt33.com/2017/08/12/java-nio/)
[深入理解Java I/O模型](https://juejin.cn/post/6844903839439519758)
[深入学习JAVA -IO流详解](https://segmentfault.com/a/1190000023008389)
