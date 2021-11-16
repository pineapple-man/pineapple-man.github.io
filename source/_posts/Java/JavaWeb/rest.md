---
title: REST
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories:
  - Java
tags: Web
keywords: Rest
excerpt: 大家常说的 Rest 是什么意思？为什么需要使用 Rest？
date: 2021-11-16 19:10:17
thumbnailImage:
---
<!-- toc -->

## 概述

REST ( REpresentational State Transfer )**(资源)表现层状态转化**,是目前流行的一种互联网**软件架构**风格，具有以下特点：

{% image fancybox fig-100  center  "https://gitee.com/mingchaohu/blog-image/raw/master/image/Rest 特点.png" %}


### 资源（Resource）

:thinking:什么是资源？

{% alert success no-icon%}

网络上的一个实体，或网络上的一个具体信息均表示资源。资源可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个在互联网中的具体存在
{%endalert%}

:notes:通过访问资源的 URI 就可以获取对应的资源，因此 URI 即为每一个资源的独一无二的识别符，{% post_link Web/HTTP/2 "查看 HTTP 资源的详细信息" %}
### 表现层（Representation）

{% alert success no-icon%}
将资源具体呈现出来的形式，称为表现层，常见的资源表示形式：txt 格式、HTML 格式、XML 格式、JSON 格式

{%endalert%}
### 状态转化（State Transfer）

每发出一个请求，就代表了客户端和服务器的一次交互过程，{% hl_text red %}由于 HTTP 协议是一个无状态协议，所以交互过程中的状态最终都保存在服务器端{% endhl_text %}。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生**状态转化(State Transfer)**。而这种转化是建立在资源表现之上，所以称为表现层状态转化

> 使用 HTTP 协议进行举例是因为：HTTP 是目前互联网在应用层交互使用最多的一种协议
## REST 到底是什么？

前面讲述了 REST 包含的几个概念，那么到底什么是 REST ，其实仍然还不太清楚。在了解 REST 是什么之前，先考虑这样一个问题：**传统方式下，如何进行前后端的资源转化？**
### 传统方式操作资源 

传统操作资源的方式，就是通过 URL 中访问的资源名来了解操作的行为，比如下表：
| URL |   含义   |
| :---------------: | :------: |
|        `http://127.0.0.1/item/queryUser.action?id=1`        | 进行查询 |
|       `http://127.0.0.1/item/saveUser.action`        | 进行新增  |
|        `http://127.0.0.1/item/updateUser.action`        | 进行更新  |
|      `http://127.0.0.1/item/deleteUser.action?id=1`       | 进行删除 |

{% alert info no-icon%}

分别通过 URL 中不同的资源名（`queryUser`,`saveUser`,`updateUser`,`deleteUser`）来做到资源的转化，在小型的项目中使用没有任何问题，但是在计算机中，几乎随着问题规模增加，总是会出现质的变化。比如，后端中如果这类资源非常多，显而易见的一个问题就是：不容易管理，前后端联调复杂度极具增加，当稍不留心，修改一个重要类后，可能造成资源的访问失败

{%endalert%}

### 请求方式

上面主要存在的问题就是：资源过多时，对这些资源的状态转化很难管理。为了解决这种问题，就提出了 REST 这样一种软件架构，其实也就是一种思想，类似于接口的概念，具体如何实施是后面 RESTful 的事情。

:question:REST 是如何解决资源转换问题的？
{% alert success no-icon%}

HTTP 协议中存在 `GET`、 `POST`、 `PUT`、 `PATCH`、 `DELETE` 等众多请求方式，用来表示客户端对服务端进行的操作。 REST 就将这些请求方式利用起来，不同的方式表示对服务端的资源进行不同的操作。其中，GET 用于查询资源，POST 用于创建资源，PUT 用于更新服务端的资源的全部信息，PATCH 用于更新服务端的资源的部分信息，DELETE 用于删除服务端的资源。

{%endalert%}
| HTTP 协议操作动词 |   含义   |
| :---------------: | :------: |
|        GET        | 获取资源 |
|       POST        | 新建资源 |
|        PUT        | 更新资源 |
|      DELETE       | 删除资源 |



### 使用 RESTful 操作资源

这里做出对传统方式操作资源的重构，通过 GET、 POST、 PUT、 PATCH、 DELETE 等方式对服务端的资源进行操作。

|  请求  |     URL     |          含义          |
| :----: | :---------: | :--------------------: |
|  GET   |   /users    |    查询用户信息列表    |
|  GET   | /users/1001 |    查看某个用户信息    |
|  POST  |   /users    |      新建用户信息      |
|  PUT   | /users/1001 | 更新用户信息(全部字段) |
| PATCH  | /users/1001 | 更新用户信息(部分字段) |
| DELETE | /users/1001 |      删除用户信息      |

## REST 与 RESTful

:thinking:之前提到了 RESTful，那么什么是 RESTful？它与 REST 是什么关系？

{% alert success no-icon%}
基于 REST 构建的 API 就是 RESTful ，RESTful 是一种建立在 REST 上的代码规范，可以将 REST 表示为接口，RESTful 表示为接口的实现
{%endalert%}

:thinking:为什么使用 RESTful

{% alert success no-icon%}

JSP 技术可以让我们在页面中嵌入 Java 代码，但是这样的技术实际上限制了开发效率，因为需要 Java 工程师将 html 转换为 jsp 页面，并写一些脚本代码，或者前端代码。这样会严重限制开发效率，也不能让 java 工程师专注于业务功能的开发，所以目前越来越多的互联网公司开始实行前后端分离。近年随着移动互联网的发展，各种类型的 Client 层出不穷，RESTful 可以通过一套统一的接口为 Web，iOS 和 Android 提供服务。另外对于广大平台来说，对开发人员提供一套完备的接口，让开发人员使用，调用相应平台的服务。比如微博开放平台，微信开放平台等，可以通过开发的接口完成相应的功能
{%endalert%}

## 如何设计 RESTful 风格的 API

既然 RESTful API 是基于 REST，那么到底应该如何设计 RESTful API 的接口？
#### 使用名词而不是动词

{% alert success no-icon%}

在 RESTful 架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表名对应，一般来说，数据库中的表都是同种记录的**集合（collection）**，所以API中的名词也应该使用复数

{%endalert%}

举例来说，有一个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样：
{% alert warning no-icon%}

https://api.example.com/v1/zoos
https://api.example.com/v1/animals
https://api.example.com/v1/employees

{%endalert%}

#### GET 方法和查询参数不应该涉及状态改变

{% alert success no-icon%}

使用 **PUT, POST** 和 **DELETE** 方法来改变状态，不要使用 **GET** 进行状态改变，GET 请求方式仅仅进行资源的获取

{%endalert%}

#### 使用复数名词

不要混淆名词单数和复数，为了保持简单，对所有资源使用复数：

{% alert warning no-icon%}

/cars 而不是 /car
/users 而不是 /user
/products 而不是 /product
/settings 而部署 /setting

{%endalert%}
#### 使用子资源表达关系

如果一个资源与另外一个资源有关系，使用子资源：

{% alert success no-icon%}

GET /cars/711/drivers/ 返回 car 711的所有司机
GET /cars/711/drivers/4 返回 car 711的4号司机

{%endalert%}

#### 为集合提供过滤、排序、选择和分页等功能

**Filtering** 过滤:

使用唯一的查询参数进行过滤：
{% alert warning no-icon%}

`GET /cars?color=red` 返回红色的cars

{%endalert%}

**Sorting** 排序:

允许针对多个字段排序
{% alert warning no-icon%}

访问下面的资源将会返回根据生产者降序和模型升序排列的car集合：`GET /cars?sort=-manufactorer,+model`
{%endalert%}


**Field selection**

移动端能够显示其中一些字段，其实不需要一个资源的所有字段，给API消费者一个选择字段的能力，这会降低网络流量，提高API可用性
{% alert warning no-icon%}

比如：`GET /cars?fields=manufacturer,model,id,color`，移动端就会自主的选择相应的字段，这里就选择了`manufacturer`、`model`、`id`和`color`字段
{%endalert%}

**Paging分页**
{% alert warning no-icon%}

使用 limit 和offset.实现分页，缺省limit=20 和offset=0；`GET /cars?offset=10&limit=5`，为了将总数发给客户端，使用订制的HTTP头： X-Total-Count.
{%endalert%}

#### 版本化 API

版本化 API 能够方便的进行 API 的版本控制，不要发布无版本的 API 项目庞大之后不容易管理，使用简单数字，避免小数点如`2.5`，一般在 Url 后面使用 `?v`，或者如下方法`/blog/api/v1`

#### 允许覆盖 HTTP 方法

一些代理只支持 **POST** 和 **GET** 两种请求方式，为了使这些有限方法支持 RESTful API，使用订制的HTTP头 **X-HTTP-Method-Override** 来覆盖POST 方法，最终根据`X-HTTP-Method-Override`头部信息，进行请求方式的判断

## 附录

[理解本真的REST架构风格](http://kb.cnblogs.com/page/186516/)
[深入浅出 REST](https://www.infoq.cn/article/rest-introduction/)
[rest和restful的区别](https://blog.csdn.net/qq_27026603/article/details/82012277)

