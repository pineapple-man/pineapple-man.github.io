---
title: Java TCP/IP Socket 编程（一）基本套接字
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: JDK
keywords: 网络
excerpt: 'Java 作为应用层程序，并不会与 C 一样，与协议接触的非常紧凑，并且本章主要是对 Java 中基本套接字的学习'
date: 2021-12-31 22:44:02
thumbnailImage:
---
<!-- toc -->

## 网络基础
:sparkles: UDP(用户数据包协议)，明显的特点如下：
{% alert success no-icon%}
- 将数据打包(最大不超过64K)
- 不建立连接
- 速度快
- 不可靠
{%endalert%}

:sparkles: TCP 协议，具有如下特点：
{% alert success no-icon%}
- 建立连接通道
- 数据无限制
- 速度慢
- 可靠
{%endalert%}


socket（套接字）是一种抽象层，应用程序通过它来发送和接受数据，就像应用程序打开一个文件句柄，将数据读写到稳定的存储器上一样。使用 socket 可以将应用程序添加到网络中，并与处于同一个网络中的其他应用程序进行通信。一台计算机上的应用程序向 socket 写入的信息能够被另一台计算机上的另一个应用程序读取，反之亦然。

不同类型的 socket 与不同类型的底层协议族以及同一协议族中的不同协议栈相关联，目前 TCP/IP 协议族中的主要 socket 类型为 流套接字（stream socket）和数据包套接字（datagram socket）

{% alert info no-icon %}
- 流套接字将 TCP 作为其端对端协议（底层使用 IP 协议），提供了一个可信赖的字节流服务。一个 TCP/IP 流套接字代表了 TCP 连接的一端
- 数据报套接字使用 UDP 协议（底层同样适用 IP 协议），提供了一个 best-effort 的数据报服务，应用程序可以通过它发送最长 65500 字节的信息。

{% endalert %}


一个 TCP/IP 套接字由一个互联网地址，一个端对端协议（TCP 协议或 UDP 协议）以及一个端口号唯一确定，下图描述了一个主机中，应用程序、套接字抽象层、协议、端口号之间的逻辑关系。{% hl_text red %} 一个套接字抽象层可以被多个应用程序引用。{% endhl_text %}
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/java/20220109001727.png %}

Socket 具有如下特点：
{% alert success no-icon%}
- 通信的两端都有`socket`
- 网络通信其实就是 Socket 间的通信
- 数据在两个 Socket 间通过 IO 流传输数据
{%endalert%}


Java Socket 常见操作
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/Java-io-socket.jpeg %}
## InetAddress类
InetAddress 类代表了一个网络目标地址，包括主机名和数字类型的地址信息。该类有两个子类：`Inet4Address`和`Inet6Address`
此类主要进行 IP 地址相关操作，{% hl_text red %} 此类没有可以访问的构造方法{% endhl_text %}

一个类没有可以访问的构造方法,可能是如下三种情况:
{% alert info no-icon%}
- 成员全部都是静态的(Math,Arrays,Collections)
- 单例模式(Runtime类)
- 类中有静态方法返回该类的对象(InetAddress)
{%endalert%}

### 成员方法

```java
//根据主机名或者IP地址的字符串得到IP地址对象,此对象包含主机名以及IP地址的信息
public static InetAddress getByName(String host)throws UnknownHostException;
//获取本机Hostname以及IP
public static InetAddress getLocalHost()throws UnknownHostException;
//获取本机主机名
public String getHostName();
//获取本机IP
public String getHostAddress();
```

不知道任何信息时，得到当前主机的主机名以及 IP 地址

```java
  public static void main(String[] args) {
    try {
      InetAddress inetAddress = InetAddress.getLocalHost();
      //将显示运行当前程序的”主机名/IP地址“
      System.out.println(inetAddress);
      //获取主机地址
      String IP = InetAddress.getLocalHost().getHostAddress();
      System.out.println(IP);
      //获取主机名
      String name = InetAddress.getLocalHost().getHostName();
      System.out.println(name);
    } catch (UnknownHostException e) {
      e.printStackTrace();
    }
  }
```

知道主机名或者 IP 地址得到另一个信息

```java
//由主机名或者IP获得当前InetAddress对象
InetAddress address = InetAddress.getByName("Hostname");
System.out.println(address.getHostName());
System.out.println(address.getHostAddress());
```

## UDP 相关 API
本小节主要记录与 UDP 相关的 API，不涉及数据质量要求时，UDP 使用的还是非常多的。Java 中与 UDP 相关的主要是如下两个类：`DatagramSocket`和`DatagramPacket`
### DatagramSocket

是 UDPSocket 的主要类

#### 构造方法

```java
//常用无参构造方法
DatagramSocket();
DatagramSocket(int port);
DatagramSocket(int port, InetAddress laddr);
```

#### 成员方法

```java
//将 UDPSocket 绑定到明确的 socket 对象上
public void bind(SocketAddress addr)throws SocketException;
public void disconnect();
public boolean isBound();
public boolean isConnected();
public boolean isClosed();
//采用 UDP 协议发送数据
public void send(DatagramPacket p)throws IOException;
//接受 UDP 协议发送来的数据，阻塞式
public void receive(DatagramPacket p)throws IOException;
```

### DatagramPacket

UDP 数据包对象，在 UDP 通信时，打包 UDP 发送的数据

#### 构造方法

```java
//接收方初始化
DatagramPacket(byte[] buf,int length);
//发送方初始化方式，指明接收方 Socket(IP,port),数据包以及数据长度
DatagramPacket(byte[] buf, int length, InetAddress address, int port);
```

#### 成员方法

```java
//返回此数据包的IP地址来源
public InetAddress getAddress();
//接收方调用返回接受端接受数据的端口号；发送方调用返回发送方的发送端口
public int getPort();
//返回发生数据的长度或者接受到的数据的长度
public int getLength();
```
### 发送数据步骤

主要步骤如下:
{% alert warning no-icon%}
1. 创建发送端 Socket 对象
2. 创建数据,并把数据打包
3. 调用 Socket 对象的发送方法发送数据包
4. 释放资源
{%endalert%}

```java
//创建一个UDPSocket对象
DatagramSocket datagramSocket = new DatagramSocket();
byte[] bytes = "hello,udp,java".getBytes();
//数据打包
DatagramPacket datagramPacket =
  //指定 localhost:10086 发送 udp 数据包
	new DatagramPacket(bytes, bytes.length, InetAddress.getLocalHost(), 10086);
//发送数据
datagramSocket.send(datagramPacket);
//关闭Socket对象，释放资源
datagramSocket.close();
```
### 接受数据步骤

UDP接受数据步骤：
{% alert warning no-icon%}
1. 创建接收端Socket对象
2. 创建一个数据包(接受容器)
3. 调用Socket对象的接收方法接受数据
4. 解析数据包，并显示在控制台
5. 释放资源

{%endalert%}

```java
  public static void main(String[] args) throws IOException {
    // 创建接受端 Socket 对象
    DatagramSocket datagramSocket = new DatagramSocket(10086);
    // 创建接受端数据包
    byte[] bytes = new byte[1024];
    DatagramPacket datagramPacket = new DatagramPacket(bytes, bytes.length);
    // 调用Socket对象的接受方法
    datagramSocket.receive(datagramPacket);
    // 解析数据
    String address = datagramPacket.getAddress().getHostAddress();
    String data = new String(datagramPacket.getData(), 0, datagramPacket.getLength());
    System.out.println(address + "data is :" + data);
    // 释放资源
    datagramSocket.close();
  }
```

## TCP 相关 API

TCP 协议中的发送数据方称为客户端(Client)，接收数据方称为服务端(Server)

### Client 方 API
TCP 协议 Client 端的 Socket 对象
#### 构造方法
```java
Socket();//创建一个未建立连接的Socket
Socket(InetAddress address, int port);//创建一个<address,port>建立连接的socket对象
Socket(String host, int port);//host可以是ip地址的格式
```
#### 成员方法

```java
//获取当前TCP数据流,而后使用I/O流写数据
public OutputStream getOutputStream()throws IOException;
public InputStream getInputStream()throws IOException;
public InetAddress getInetAddress();
public void shutdownInput()throws IOException;
public void shutdownOutput()throws IOException;
```
#### TCP 报文接受数据步骤
{% alert warning no-icon%}
1. 创建接受端的 Socket 对象
2. 监听客户端,返回一个对应的 Socket 对象
3. 获取输入流，读取数据显示在控制台
4. 释放资源
{%endalert%}

```java
public static void main(String[] args) throws IOException {
  //创建接受端的Socket对象
  ServerSocket serverSocket = new ServerSocket(10086);
  //侦听并接受此套接字，此方法在连接建立之前一直阻塞
	Socket accept = serverSocket.accept();
  //获取输入流
  InputStream inputStream = accept.getInputStream();
  byte[] bytes = new byte[1024];
  //阻塞式方法
  int len = inputStream.read(bytes);
  System.out.println(
	accept.getInetAddress().getHostAddress() + "-----" + new String(bytes, 0, len));
  //释放资源
  accept.close();
}
```
### Server 方 API
TCP 协议 Server 端 Socket 对象

#### 构造方法
```java
//创建一个没有绑定的服务端Socket
ServerSocket();
//创建一个本机监听特定端口的服务器端Socket
ServerSocket(int port);

```

#### 成员方法

```java
//监听客户端，返回一个对应的Socket对象
public Socket accept()throws IOException;
//获取当前Socket对象的输入流数据
public InputStream getInputStream()throws IOException;
```

#### TCP报文发送流程

{% alert success no-icon%}
1. 创建发送端的 Socket 对象，这一步如果成功，说明连接已经建立成功
2. 获取数据流，写数据
3. 释放资源
{%endalert%}

:notes:`java.net.ConnectException：连接被拒绝`，TCP 协议一定要先打开服务器，而后发送数据，否则连接建立失败

```java
public static void main(String[] args) throws IOException {
  // 创建 TCPsocket 对象
  Socket socket = new Socket(InetAddress.getLocalHost(), 10086);
  // 获取输出数据流
  OutputStream outputStream = socket.getOutputStream();
  outputStream.write("hello tcp !".getBytes());
  socket.close();
}
```