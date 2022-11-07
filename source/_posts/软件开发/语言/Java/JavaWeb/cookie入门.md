---
title: cookie 介绍
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2021-10-24 21:35:19
thumbnailImage: https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/cookie.jpg
categories:
  - Java
tags:
  - Web
keywords: cookie
excerpt: 本文详细讲解在web开发中经常使用的Cookie技术
---

<!-- toc -->

## Cookie 的起源

:question:为什么我们需要`Cookie`?

- 早期**Web 开发面临的最大问题之一是如何管理状态**，服务器没有办法知道两个请求是否来自于同一个浏览器，那时的办法是在请求的页面中插入一个 token，并且在下一次请求中将这个 token 返回（至服务器）。这就需要在 form 中插入一个包含 token 的隐藏表单域，或者在 URL 的 qurey 字符串中传递该 token，这两种方法都强调手工操作并且极易出错
- 为了避免人为增加状态管理带来的错误率，使用`Cookie`和`Session`两种技术进行 Web 开发中的**状态管理，能够让服务器在一段时间内认识用户**
- 状态管理的根源在于：**HTTP 协议是无连接无状态的**

## Cookie 简介

:thinking:什么是 **Cookie**?

一个 Cookie 就是存储在用户主机浏览器中的一小段**文本文件**，其中记录了用户名，密码、浏览的网页和停留时间等相关信息，**不包含任何可执行代码**，由一个 Web 页面或服务器告之浏览器存储这些信息，并且之后浏览器发送的每个请求中都将携带该信息。

Web 服务器随后可以利用这些信息来标识用户，多数需要登录的网站通常会在用户认证信息通过后来设置一个 Cookie，之后只要这个 cookie 存在并且合法，用户就可以自由的浏览这个站点的所有部分

:notebook:可以简单的认为 Cookie 就是一种**浏览器的缓存机制**，本身仅包含了数据本身并不是有害的

## Cookie 的分类

:sparkles:Cookie 保存在客户端中，**根据在客户端中的存储位置**，可分为**内存 Cookie**和**硬盘 Cookie**

- **内存 Cookie** 由浏览器维护，保存在内存中，浏览器关闭即消失，存在时间短暂
- **硬盘 Cookie** 保存在硬盘里，有过期时间，除非用户手动清理或到了过期时间，硬盘 Cookie 不会清楚，存在时间较长

## Cookie 限制

:persevere:Cookie 的缺陷

1. Cookie 会被附加在每个 HTTP 请求中，所以无形中增加了流量
2. 由于 HTTP 请求中的 Cookie 是明文传递的，所以安全性成问题，除非使用 HTTPS
3. Cookie 的大小限制在 4KB 左右，对于复杂的存储需求来说是不够用的

## Cookie 的使用

通过 HTTP 的**Set-Cookie**消息头，Web 服务器可以指定**浏览器存储一个 cookie**，`Set-Cookie`消息的格式如下面的字符串

```cookie
Set-Cookie:value [ ;expires=date][ ;domain=domain][ ;path=path][ ;secure]
```

消息头的第一部分，`value`部分，通常是一个`name=value`格式的字符串（推荐使用这种格式），但是浏览器对 cookie 的 value 字段并不会按此格式校验，因此，指定一个不包含等号的字符串仍然会被存储，然而通常的使用方式仍然是以 name=value 的格式（并且多数的接口只支持该格式）来指定 cookie 的值。

当一个 cookie 存在，**接下来的每个请求中都会将 cookie 的值发送至服务器**，cookie 的值存储在名为`Cookie`的 HTTP 消息头中，并且只包含了 cookie 的值，其他的选项全部被去除，例如：

```cookie
Cookie : value
```

通过 Set-Cookie 指定的选项只是应用于浏览器端，**一旦选项被设置后便不会被服务器重新取回**。cookie 的值与 Set-Cookie 中指定的值是完全一样的字符串；对于这些值不会有更近一步的解析或转码操作。如果在指定的请求中有多个 cookies，那么它们会被分号和空格分开，例如：

```cookie
Cookie:value1 ; value2 ; name1=value1
```

![cookie](https://upload.wikimedia.org/wikipedia/commons/b/bc/HTTP_cookie_exchange.svg)

### cookie 编码（cookie encoding）

:sparkles: cookie 编码特点

- 规范中指出仅有**三种类型的字符必须进行编码**：**分号**，**逗号**，和**空格**
- 规范中提到**可以利用 URL 编码，但是并不是必须**，RFC 没有提及任何的编码
- 对于 name=value 的格式，name 和 value 通常都单独进行编码并且不对等号“=”进行编码操作

### 有效期选项（The expires option）

紧跟 `cookie value` 后面的每个选项都以**分号和空格分割**，第一个选项是 expires，，其指定了 cookie 何时不会再被发送到服务器端的，因此该 cookie 可能会被浏览器删掉。该选项所对应的值是一个格式为`Wdy,DD-Mon--YYYY HH:MM:SS GMT`的值，例如：

```cookie
Set-Cookie:name=Nicholas; expires=Sat， 02 May 2009 23:38:25 GMT
```

:sparkles: cookie 有效期特点

- 在没有 expires 选项时，cookie 的寿命仅限于单一的会话中，浏览器的关闭意味这一次会话的结束，会话结束时会将 cookie 一并删除
- 如果 expires 选项设置了一个过去的时间点，那么这个 cookie 会被立即删除

### domain 选项（The domain option）

domain 选项指示 cookie 将要发送到哪个域或那些域中。默认情况下，domain 会被设置为创建该 cookie 的页面所在的域名。例如本站中的 cookie 的 domain 属性的默认值为`.pineapple-man.github.io`

```cookie
Set-Cookie:name=Nicholas; domain=github.io
```

:sparkles: domain 选项特点

- **domain 选项被用来扩展 cookie 值所要发送域的数量**，诸如`Yahoo!`，这样的大型网站都会有许多以 name.yahoo.com 格式的站点。可以将最大共同匹配的路径作为 cookie 中 domain 的值，随后将此 cookie 发送给浏览器
- 浏览器会对**domain 的值与请求发送至的域名，做一个尾部比较**，并且在匹配后发送一个 Cookie 消息头
- domain 设置的值必须是发送 Set-Cookie 消息头的域名。例如，我无法向`google.com`发送一个 cookie，因为这个将会产生安全问题，不合法的 domain 选项，将会被浏览器忽略

### Path 选项（The path option）

另一个控制何时发送 Cookie 消息头的方式是指定 path 选项。与 domain 选项相同的是，path 指明了在发 Cookie 消息头之前必须存在一个 URL 路径。这个比较是通过将 path 属性值与请求的 URL**从头开始匹配**的，如果字符匹配，则发送 Cookie 消息头，例如：

```cookie
Set-Cookie:name=Nicholas; path=/blog
```

在这个例子中，path 选项值会与/blog，/blogrool 等等相匹配；任何以/blog 开头的选项都是合法的。

:notes:要注意的是**只有在 domain 选项核实完毕之后才会对 path 属性进行比较**，path 属性的默认值是发送 Set-Cookie 消息头所对应的 URL 中的 path 部分

### secure 选项（The secure option）

该选项只是**一个标记**，只有当请求是通过 SSL 和 HTTPS 创建时，才是一个 secure cookie，并允许发送到服务器端。这种以纯文本形式传输的 secure cookie 具有很高的价值但可能存在被破解的风险

```cookie
Set-Cookie:name=Nicholas; secure
```

:notes:实际开发中**机密信息绝不应该在 cookies 中存储或传输**，因为 cookies 本身就是没有考虑安全性，默认情况下，在 HTTPS 链接上传输的 cookies 都会被自动添加上 secure 选项；如果网页使用的是`http`协议，无法设置`secure`类型的 cookie

### cookie 的维护和生命周期（cookie maintenance and lifecycle）

任意数量的选项都可以在单一的 cookie 中指定，并且这些选项可以以任何顺序存在，例如

```cookie
Set-Cookie:name=Nicholas; domain=github.io; path=/blog
```

这个 cooke 有四个标识符：cookie 的 name，domain，path，secure 标记。要想在将来改变这个 cookie 的值，需要发送另一个具有相同 cookie name，domain，path 的 Set-Cookie 消息头，例如

```cookie
Set-Cooke:name=Greg; domain=github.io; path=/blog
```

这将以一个新的值来覆盖原来 cookie 的值，然而，仅仅改变这些选项的某一个值将会创建一个完全不同的 cookie，例如：

```cookie
Set-Cookie:name=Nicholas; domain=github.io; path=/
```

在返回这个消息头后，会同时存在两个不同的 cookie，如果你访问`pineapple-man.github.io/blog`下的一个页面，以下的消息头将被包含进来

```cookie
Cookie：name=Greg; name=Nicholas
```

这个消息头中存在了两个 cookie，path 值越详细则 cookie 越靠前。domain-path 越详细则 cookie 字符串越靠前。假设当前我正在访问：`pineapple-man.github.io/blog`并且发送了另一个 cookie

```cookie
Set-Cookie:name=Mike
```

那么返回的消息头现在则变为：

```cookie
Cookie：name=Mike;name=Greg;name=Nicholas
```

由于包含“Mike”的 cookie 使用了域名`pineapple-man.github.io`作为其 domain 值并且以全路径`/blog`作为其 path 值，它比其他两个 cookie 更加详细所以更加靠前

### 使用失效日期（using expiration dates）

当 cookie 创建时包含了失效日期，这个失效日期则关乎 cookie 的生命周期。
要改变一个 cookie 的失效日期，必须指定同样的组合，当改变一个 cookie 的值时，你不必每次都设置失效日期，因为它不是 cookie 标识信息的组成部分。例如：

```cookie
Set-Cookie:name=Mike; expires=Sat，03 May 2025 17:44:22 GMT
```

现在已经设置了 cookie 的失效日期，下次想要改变 cookie 的值时，只需要使用它的名字：

```cookie
Set-Cookie:name=Matt
```

在 cookie 上的失效日期并没有改变，因为 cookie 的标识符是相同的。实际上，只有你手工的改变 cookie 的失效日期，否则其失效日期不会改变。这意味着在同一个会话中，一个会话 cookie 可以变成一个持久化 cookie（一个可以在多个会话中存在的），反之则不可。

如果想将一个**持久化 cookie 变为会话 cookie**，必须删除这个持久化 cookie，随后再创建一个同名的会话 cookie 就可以实现

:question:如何删除一个 cookie？

- 只需要将 cookie 设置为过去的某个时间就可以

:notes:失效日期是以浏览器运行的电脑上的系统时间为基准进行核实的。没有任何办法来来验证这个系统时间是否和服务器的时间同步，所以当服务器时间和浏览器所处系统时间存在差异时这样的设置会出现错误

### cookie 自动删除（automatic cookie removal）

通常存在以下几种原因，浏览器会自动删除 cookie：

- 会话结束时（浏览器关闭）将会删除会话 cooke(Session cookie)
- 到达失效日期时将会删除持久化 cookie（Persistent cookie）
- 浏览器中的 cookie 限制到达允许存储最大 cookie 数，会删除 cookies

:notebook:对于任何这些自动删除来说，Cookie 管理显得十分重要，因为这些删除都是无意识的，如果不进行管理将造成意想不到的结果

### Cookie 限制条件（Cookie restrictions）

:dart:cookies 的限制条件是为了**阻止 cookie 滥用**并**保护浏览器和服务器安全**
:sparkles:有两种 cookies 的限制条件：**cookies 的属性**和**cookies 的总大小**

- 原始的规范中限定每个域名下不超过 20 个 cookies，早期的浏览器都遵循该规范，并且在 IE7 中有个更近一步的提升。在微软的一次更新中，IE7 增加 cookie 的限制到 50 个，与此同时 opera 限定 cookie 个数为 30 个，safari 和 chrome 对每个域名下的 cookie 个数没有限制
- 发向服务器的所有 cookies 的最大容量仍旧是 4KB。所有超出该限制的 cookies 都会被截掉并且不会发送至服务器

### subcookie

由于部分浏览器中 cookie 的数量限制，开发者提出的**subcookies**的观点来增加 cookies 的存储量，**Subcookies 是一些存储在一个 cookie 的 value 中的一些 name-value 对**，并且通常与以下格式类似：

```cookie
name=a=b&c=d&e=f&g=h
```

这种方式允许在单个 cookie 中保存多个 name-value 对，而不会超过浏览器 cookie 的数量限制。通过这种方式创建 cookies 的负面影响是：**需要自定义解析方式来提取这些值**

### HTTP-Only cookie

微软 IE6 SP1 在 cookies 中引入了一个新的选项：**HTTP-only cookies**.HTTP-Only 背后的意思是告诉浏览器**该 cookie 绝不应该通过 Javascript 的 document.cookie 属性访问**。
:dart:设计该特征的意图是：**提供一个安全措施来阻止跨站脚本攻击(XSS)窃取 cookie 的行为**

:question:如何将 cookie 设置为 HTTP-Only？

- 要创建一个 HTTP-Only cookie，只要向 cookie 中添加一个 HTTP-Only 标记即可

```cookie
Set-Cookie: name=Nicholas; HttpOnly
```

一旦设定这个标记，通过`documen.coookie`则不能再访问该 cookie。并且不允许通过`XMLHttpRequest`的`getAllResponseHeaders()`或`getResponseHeader()`方法访问 cookie

## 总结

为了高效的利用 cookies，仍旧有许多要了解和弄明白的东西。本篇只是提供了一些每个人都应该知道的关于浏览器 cookies 的基本知识，也不是一个完整的参考。**对于今天的 web 来说 Cookies 仍旧起着非常重要的作用，并且不恰当的管理 cookies 会导致各种安全性的问题**

## 附录

[聊一聊 Cookie](https://segmentfault.com/a/1190000004556040)

[HTTP cookies explained](https://humanwhocodes.com/blog/2009/05/05/http-cookies-explained/)

[HTTP cookie](https://en.wikipedia.org/wiki/HTTP_cookie)
