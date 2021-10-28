---
title: WebSocket
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java Web
tags: Web
keywords: WebSocket
excerpt: 本文主要讲解WebSocket协议，及 WS 协议与 HTTP 协议有什么不同
---
## 概述

:question:什么是`WebSocket`？

WebSocket是一种网络传输协议，可在**单个TCP连接上进行全双工通信，位于OSI模型的应用层**

:sparkles:WebSocket协议特点

- WebSocket使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据
- 在WebSocket API中，浏览器和服务器只需要完成一次握手，两者之间就可以创建持久性的连接，并进行双向数据传输

:vs:WebSocket与HTTP有什么不同？

- WebSocket是一种与HTTP不同的协议，两者都位于OSI模型的应用层，并且都依赖于传输层的TCP协议，虽然他部门不同，但是RFC 6455中规定

> it is designed to work over HTTP ports 80 and 443 as well as to support HTTP proxies and intermediaries
>
> WebSocket通过HTTP端口80和443进行工作，并支持HTTP代理和中介，从而使其与HTTP协议兼容

WebSocket协议支持Web浏览器（或其他客户端应用程序）与Web服务器之间的交互，具有较低的开销，便于实现客户端与服务器的实时数据传输。

服务器可以通过标准化的方式来实现，而无需客户端首先请求内容，并允许消息在保持连接打开的同时来回传递。

通过这种方式，可以在客户端和服务器之间进行双向持续对话。 通信通过TCP端口80或443完成，这在防火墙阻止非Web网络连接的环境下是有益的

与HTTP不同，WebSocket提供全双工通信，WebSocket还可以在TCP之上实现消息流。TCP单独处理字节流，没有固有的消息概念。 在WebSocket之前，使用Comet可以实现全双工通信。但是Comet存在TCP握手和HTTP头的开销，因此对于小消息来说效率很低。WebSocket协议旨在解决这些问题。

WebSocket协议规范将`ws`（WebSocket）和`wss`（WebSocket Secure）定义为两个新的统一资源标识符，分别对应明文和加密连接，

## WebSocket 协议

WebSocket并不是全新的协议，而是利用了HTTP协议来建立连接。

首先，WebSocket连接必须由浏览器发起，因为请求协议是一个标准的HTTP请求，格式如下：

```http
GET ws://localhost:3000/ws/chat HTTP/1.1
Host: localhost
Upgrade: websocket
Connection: Upgrade
Origin: http://localhost:3000
Sec-WebSocket-Key: client-random-string
Sec-WebSocket-Version: 13
```

请求和普通的HTTP请求有几点不同：

1. GET请求的地址不是类似`/path/`，而是以`ws://`开头的地址；
2. 请求头`Upgrade: websocket`和`Connection: Upgrade`表示这个连接将要被转换为WebSocket连接；
3. `Sec-WebSocket-Key`是用于标识这个连接，并非用于加密数据；
4. `Sec-WebSocket-Version`指定了WebSocket的协议版本。

随后，服务器如果接受该请求，就会返回如下响应

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: server-random-string
```

该响应代码`101`表示本次连接的HTTP协议即将被更改，更改后的协议就是`Upgrade: websocket`指定的WebSocket协议

版本号和子协议规定了双方能理解的数据格式，以及是否支持压缩等等。如果仅使用WebSocket的API，就不需要关心这些

现在，一个WebSocket连接就建立成功，浏览器和服务器就可以随时主动发送消息给对方。消息有两种，一种是文本，一种是二进制数据。通常，我们可以发送JSON格式的文本，这样，在浏览器处理起来就十分容易

为什么WebSocket连接可以实现全双工通信而HTTP连接不行呢？实际上HTTP协议是建立在TCP协议之上的，TCP协议本身就实现了全双工通信，但是HTTP协议的请求－应答机制限制了全双工通信。WebSocket连接建立以后，其实只是简单规定了一下：接下来，咱们通信就不使用HTTP协议了，直接互相发数据吧

安全的WebSocket连接机制和HTTPS类似。首先，浏览器用`wss://xxx`创建WebSocket连接时，会先通过HTTPS创建安全的连接，然后，该HTTPS连接升级为WebSocket连接，底层通信走的仍然是安全的SSL/TLS协议

## 使用者

### 浏览器

很显然，要支持WebSocket通信，浏览器得支持这个协议，这样才能发出`ws://xxx`的请求。目前，支持WebSocket的主流浏览器如下：

- Chrome
- Firefox
- IE >= 10
- Sarafi >= 6
- Android >= 4.4
- iOS >= 8

### 服务器

由于WebSocket是一个协议，服务器具体怎么实现，取决于所用编程语言和框架本身

## 附录

[WebSocket——廖雪峰](https://www.liaoxuefeng.com/wiki/1022910821149312/1103303693824096)

[WebSocket——wiki](https://zh.wikipedia.org/wiki/WebSocket)