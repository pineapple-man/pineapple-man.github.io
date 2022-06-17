---
title: Channel
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: NIO
keywords: Channel
excerpt: 本文主要讲述 NIO 中与 Channel 相关的知识
date: 2022-04-21 00:33:06
thumbnailImage:
---
<!-- toc -->

常见的 channel 有:`FileChannel`,`DatagramChannel`,`SocketChannel`,`ServerSocketChannel`
## 概述

通道（Channel）：由 java.nio.channels 包定义 的。Channel 表示 IO 源与目标打开的连接。 Channel 类似于传统的“流”。只不过 Channel 本身不能直接访问数据，Channel 只能与 Buffer 进行交互

:vs:通道与流的区别：

- 通道可以同时进行读写，而流只能读或者只能写
- 通道可以实现异步读写数据
- 通道可以从缓冲读数据，也可以写数据到缓冲

Channel 在 NIO 中是一个接口

```java
public interface Channel extends Closeable{}
```

## 常用的 Channel 实现类

|        实现类         |                             含义                             |
| :-------------------: | :----------------------------------------------------------: |
|     `FileChannel`     |             用于读取、写入、映射和操作文件的通道             |
|   `DatagramChannel`   |                通过 UDP 读写网络中的数据通道                 |
|    `SocketChannel`    |                  通过 TCP 读写网络中的数据                   |
| `ServerSocketChannel` | 可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel |

:sparkles:ServerSocketChannel 类似 ServerSocket , SocketChannel 类似 Socket

## 两个 Channel 传输数据
```java
	public static void main(String[] args) throws IOException {
		String from = "/Users/humingchao/Documents/information/Netty教程源码资料/代码/netty-demo/src/main/resources/logback" +
				".xml";
		String to = "./copy.xml";
		long start = System.nanoTime();
		try (FileChannel inputChannel = new FileInputStream(from).getChannel()) {
			FileChannel outputChannel = new FileOutputStream(to).getChannel();
			inputChannel.transferTo(0, inputChannel.size(), outputChannel);
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		}
		long end = System.nanoTime();
		System.out.println("transfer spend " + (end - start) + " nano time");
	}
```