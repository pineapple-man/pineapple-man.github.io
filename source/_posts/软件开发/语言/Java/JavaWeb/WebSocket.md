---
title: WebSocket
toc: true
clearReading: true
metaAlignment: center
categories:
  - Java
tags: Web
keywords: WebSocket
excerpt: 本文主要讲解WebSocket协议，及 WS 协议与 HTTP 协议有什么不同
date: 2021-10-30 21:51:46
---

<!-- toc -->

## 概述

:question:什么是`WebSocket`？

{% alert success no-icon %}
WebSocket 是一种网络传输协议，可在**单个 TCP 连接上进行全双工通信，位于 OSI 模型的应用层**，并且将`ws`（WebSocket）和`wss`（WebSocket Secure）定义为两个新的统一资源标识符，分别对应明文和加密连接，
{% endalert %}

:sparkles:WebSocket 协议特点

- 使客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据
- 浏览器和服务器只需要完成一次握手，两者之间就可以创建持久性的连接，并进行双向数据传输
- 由于 TCP 单独处理字节流，没有固有的消息概念，WS 可以在 TCP 之上实现消息流

:vs:WebSocket 与 HTTP 有什么异同？

{% alert success no-icon %}
WebSocket 是一种与 HTTP 不同的协议，两者都位于 OSI 模型的应用层，并且都依赖于传输层的 TCP 协议
WebSocket 通过 HTTP 端口 80 和 443 进行工作，并支持 HTTP 代理和中介，从而使其与 HTTP 协议兼容
{% endalert %}

## WebSocket 协议

WebSocket 并不是全新的协议，而是利用了 HTTP 协议来建立连接，因此和 HTTP 协议相同，在建立 WS 连接时使用 HTTP 的请求与响应过程，连接建立之后就可以使用 WS 进行双工通信

### 请求

:notes:WebSocket 连接必须由浏览器发起，并且请求协议是一个标准的 HTTP 请求，格式如下：

```http
GET ws://localhost:3000/ws/chat HTTP/1.1
Host: localhost
Upgrade: websocket
Connection: Upgrade
Origin: http://localhost:3000
Sec-WebSocket-Key: client-random-string
Sec-WebSocket-Version: 13
```

:notes:WS 请求和普通的 HTTP 请求有几点不同：

1. GET 请求的地址不是类似`/path/`，而是以`ws://`开头的地址；
2. 请求头`Upgrade: websocket`和`Connection: Upgrade`表示这个连接将要被转换为 WebSocket 连接；
3. `Sec-WebSocket-Key`是用于标识这个连接，并非用于加密数据；
4. `Sec-WebSocket-Version`指定了 WebSocket 的协议版本。

### 响应

服务器如果接受该请求，就会返回如下响应

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: server-random-string
```

该响应代码`101`表示本次连接的 HTTP 协议即将被更改，更改后的协议就是`Upgrade: websocket`指定的 WebSocket 协议，版本号和子协议规定了双方能理解的数据格式，以及是否支持压缩等等

现在，一个 WebSocket 连接就建立成功，浏览器和服务器就可以随时主动发送消息给对方。消息有两种，一种是**文本**，一种是**二进制数据**

:question:为什么 WebSocket 连接可以实现全双工通信而 HTTP 连接不行呢？

{% alert success no-icon%}
实际上 HTTP 协议是建立在 TCP 协议之上的，TCP 协议本身就实现了全双工通信，但是 HTTP 协议的请求－应答机制限制了全双工通信。WebSocket 连接建立以后，其实只是简单的规定，接下来，咱们通信就不使用 HTTP 协议了，直接互相发数据进行通信
{% endalert %}

:sparkles:安全的 WebSocket 连接机制和 HTTPS 类似。首先，浏览器用`wss://xxx`创建 WebSocket 连接时，会先通过 HTTPS 创建安全的连接，然后，该 HTTPS 连接升级为 WebSocket 连接，底层通信走的仍然是安全的 SSL/TLS 协议

## 使用者

要支持 WebSocket 通信，浏览器需要支持这个协议，这样才能发出`ws://xxx`的请求。目前，支持 WebSocket 的主流浏览器如下：

- Chrome
- Firefox
- IE >= 10
- Sarafi >= 6
- Android >= 4.4
- iOS >= 8

对于服务器是如何实现 WS，取决于所用编程语言和使用的框架

## 附录

[WebSocket——廖雪峰](https://www.liaoxuefeng.com/wiki/1022910821149312/1103303693824096)

[WebSocket——wiki](https://zh.wikipedia.org/wiki/WebSocket)
