---
title: CDN详解
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 高并发
tags: Web
keywords: CDN
excerpt: 一直都知道存在CDN但是底层原理是什么，是如何实现的一直处于懵懂状态，本文搜集网上找到的资料，记录CDN的相关知识
date: 2021-11-10 13:27:59
thumbnailImage:
---

<!-- toc -->

## 概述

:thinking:什么是 CDN

{% alert success no-icon%}
`CDN（内容分发网络）`是 `Content Delivery Network`，建立并覆盖在承载网之上、由分布在不同区域的边缘节点服务器群组成的分布式网络，替代传统以 `WEB Server` 为中心的数据传输模式
{% endalert %}

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_1.gif %}

:thinking:CDN 有什么用？

{% alert success no-icon%}

将源内容发布到边缘节点，配合精准的**调度系统**；<font style="color:red;font-weight:bold">将用户的请求分配至最适合它的节点，使用户可以以最快的速度取得他所需的内容，有效解决 Internet 网络拥塞状况，提高用户访问的响应速度</font>

{% endalert %}

:thinking:镜像服务器也完全可以做到内容的拷贝，提供给各个用户使用，为什么还需要 CDN？

{% alert success no-icon%}

镜像服务器是**源内容服务器的完整复制**，CDN 是部分内容的缓存，智能程度更高。

{% endalert %}

:persevere:CDN 并不是万能的，在部分场景下，CDN 并不是适用

{% alert success no-icon%}

- CDN 适用于静态的内容，**不适用动态的内容**。用户动态的实时交互数据，是难以缓存的，例如：一些频繁修改的数据库表单内容等
- 很多应用提供商和内容服务商，为了**保护自身的数据私密**，不允许第三方公司 CDN 缓存他们的数据，只允许自家 CDN 缓存自家的数据。这个对用户体验会造成一定影响
- 建设 CDN 意味着**不菲的资金投入**。不管是自己买服务器搭建 CDN，还是租用云服务提供商的 CDN 服务，都需要花钱。而且，区域越多，花的钱越多。这些 CDN 到底有没有人用，利用率是多少，很难精准预测。也许大部分时间里，利用率很低，就造成了资源浪费
  {% endalert %}

{% alert info no-icon%}

可以简单的理解：**CDN=更智能的镜像服务器+缓存+流量导流**

{% endalert %}

## 为什么需要 CDN

当下的互联网应用都包含大量的静态内容，但静态内容以及一些准动态内容又是最耗费带宽的，特别是针对全国甚至全世界的大型网站，如果这些请求都指向主站的服务器的话，不仅是主站服务器受不了，单端口 500M 左右的带宽也扛不住，所以大多数网站都需要 CDN 服务

{% alert success no-icon%}
根本上的原因是：访问速度对互联网应用的用户体验、口碑、甚至说直接的营收都有巨大的影响，任何的企业都渴望自己站点有更快的访问速度。而 HTTP 传输时延对 web 的访问速度的影响很大，在绝大多数情况下是起决定性作用的，这是由 TCP/IP 协议的一些特点决定的。物理层上的原因是光速有限、信道有限，协议上的原因有丢包、慢启动、拥塞控制等
{% endalert%}
:notes:使用 CDN 的第一个也是最重要的原因：<font style="color:red;font-weight:bold">为了加速资源的访问</font>

## CDN 其他作用

CDN 除了能够加速网站访问外，还能够做到以下功能

### 实现跨运营商、跨地域的全网覆盖

互联不互通、区域 ISP 地域局限、出口带宽受限制等种种因素都造成了网站的区域性无法访问。CDN 加速可以覆盖全球的线路，通过和运营商合作，在全国骨干节点上，合理部署 CDN 边缘分发存储节点，充分利用带宽资源，平衡源站流量

### 保障网站安全

CDN 的负载均衡和分布式存储技术，可以加强网站的可靠性，相当无无形中给你的网站添加了一把保护伞，能够应对绝大部分的互联网攻击事件

### 异地备援

当某个服务器发生意外故障时，系统将会调用其他临近的健康服务器节点进行服务，进而提供接近 100%的可靠性，可以达到网站可以做到永不宕机

### 节约成本

投入使用 CDN 加速可以实现网站的全国铺设，你根据不用考虑购买服务器与后续的托管运维，服务器之间镜像同步，也不用为了管理维护技术人员而烦恼，节省了人力、精力和财力

### 能够更专注业务本身

CDN 加速厂商一般都会提供一站式服务，业务不仅限于 CDN，还有配套的云存储、大数据服务、视频云服务等，而且一般会提供 7x24 运维监控支持，保证网络随时畅通，用户可以放心使用，将更多的精力投入到发展自身的核心业务之上

## CDN 应用场景

:sparkles:CDN 的应用场景：**网站站点加速**、**视音频点播（大文件下载）分发加速**、**视频直播加速**、**移动应用加速**

### 网站站点（应用）加速

站点或应用中含有大量资源，为了能够加速资源分发，建议将站点内容进行**动静分离**，动态文件可以结合云服务器 ECS 自身做到加速，静态资源（如各类型图片、html、css、js 文件等）建议结合对象存储 OSS 存储海量静态资源，可以有效加速内容加载速度，轻松搞定网站图片、短视频等内容分发

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_2.jpeg %}

### 视音频点播（大文件下载）分发加速

CDN 支持各类文件的下载、分发，支持在线点播加速业务，如 mp4、flv 视频文件或者平均单个文件大小在 20M 以上，主要的业务场景是视音频点播、大文件下载（如安装包下载）等。搭配对象存储 OSS 使用，可提升回源速度，节约近 2/3 回源带宽成本

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_3.jpeg %}

### 视频直播加速

结合弹性伸缩服务，及时调整服务器带宽，应对突发访问流量；结合媒体转码服务，享受高速稳定的并行转码，且任务规模无缝扩展

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_4.jpeg%}

### 移动应用加速

利用 CDN 能够保证：移动 APP 更快速的做到资源（图片、页面、短视频等内容）的分发

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_5.jpeg %}

## CDN 和通信

互联网服务提供商采用 CDN，是**以存储换时延**，花钱购置 CDN 服务器或云计算服务，以此换取更好的用户体验；通信运营商也追捧 CDN，但它们的目的，是**以存储换带宽**，通过将服务“**下沉**”，减轻上层骨干网络的流量压力，避免硬件扩容，降低网络建设成本

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_6.webp %}

:sparkles:CDN 这种思想和移动边缘计算有异曲同工之妙。CDN 可以算是边缘计算的一种特殊形式，CDN 主要是存储能力和少部分计算能力的下沉，功能较为有限。真正的 MEC 边缘计算，能力更强大，功能更全面，更加偏向算力下沉，而非内容下沉

## CDN 工作过程

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_7.jpeg %}

:sailboat:用户通过浏览器等方式访问网站的过程如下图所示：

{% alert success no-icon%}

1. 用户在自己的浏览器中输入要访问的网站域名
2. 浏览器向 **本地 DNS 服务器** 请求对该域名的解析
3. 本地 DNS 服务器中如果缓存有这个域名的解析结果，则直接响应用户的解析请求
4. 本地 DNS 服务器中如果没有关于这个域名的解析结果的缓存，则以递归方式向整个 DNS 系统请求解析，获得应答后将结果反馈给浏览器
5. 浏览器得到域名解析结果，就是该域名相应的服务设备的 **IP 地址**
6. 浏览器向服务器（获得的 IP 地址）请求内容
7. 服务器将用户请求内容传送给浏览器
   {%endalert%}

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_8.jpeg %}

在网站和用户之间加入 CDN 以后，用户不会有任何与原来不同的感觉。最简单的 CDN 网络只需要有一个 DNS 服务器和几台缓存服务器就可以运行了。一个典型的 CDN 用户访问**调度流程**如图所示：

{% alert success no-icon%}

1. 当用户点击网站页面上的内容 URL，经过本地 DNS 系统解析，DNS 系统最终会将域名的解析权交给 `CNAME` 指向的 **CDN 专用 DNS 服务器**（<font style="color:red;font-weight:bold">地址解析权限转交</font>）
2. CDN 的 DNS 服务器将 CDN 的全局负载均衡设备 IP 地址返回用户
3. 用户向 CDN 的**全局负载均衡设备**发起内容 URL 访问请求
4. CDN 全局负载均衡设备根据用户 IP 地址，以及用户请求的内容 URL，选择一台用户所属区域的**区域负载均衡设备**，告诉用户向这台设备发起请求
5. 基于以下这些条件的综合分析之后，区域负载均衡设备会向全局负载均衡设备返回一台**缓存服务器**的 IP 地址：
   - 根据用户 IP 地址，判断哪一台服务器距用户最近
   - 根据用户所请求的 URL 中携带的内容名称，判断哪一台服务器上有用户所需内容
   - 查询各个服务器当前的负载情况，判断哪一台服务器尚有服务能力
6. 全局负载均衡设备把**缓存服务器的 IP 地址**返回给用户
7. 用户向**缓存服务器**发起请求，缓存服务器响应用户请求，将用户所需内容传送到**用户终端**。如果这台缓存服务器上并没有用户想要的内容，而区域均衡设备依然将它分配给了用户，那么这台服务器就要向它的上一级缓存服务器请求内容，直至追溯到网站的源服务器将内容拉到本地
   {%endalert%}

:notes:<font style="color:red;font-weight:bold">DNS 服务器根据用户 IP 地址，将域名解析成相应节点的缓存服务器 IP 地址，实现用户就近访问</font>。使用 CDN 服务的网站，只需将其域名解析权交给 CDN 的全局负载均衡（GSLB）设备，将需要分发的内容注入 CDN，就可以实现内容加速了

使用 CDN 后的**http 请求处理流程**如下图，其中左边为**DNS 解析过程**，右边为**内容访问过程**：

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_9.jpeg%}

## CDN 功能架构

CDN 基于如下初衷进行设计：
{% alert success no-icon%}
对于每个请求挑选最优设备为用户提供服务，如果某个内容被很多用户所需要，它就被缓存到距离用户最近的节点中
{%endalert%}

CDN 公司在整个互联网上部署数以百计的 CDN 服务器，这些服务器通常在运营商的 `IDC (互联网数据中心Internet Data Center）`中，**尽量靠近接入网络和用户**CDN 服务器中的内容是相同的，当内容的提供者更新内容时，CDN 服务器会统一刷新内容。

{% alert success no-icon%}
:sparkles:CDN 提供一种机制，当用户请求内容时，该内容能够以最快速度交付的 CDN 服务器向用户提供，这种**挑选最优**的过程也是一种变向的**负载均衡技术**，被选中的最优 CDN 服务器可能最靠近用户，或者有一条与用户之间条件最好的路径
{%endalert%}

从**功能**上划分，典型的 CDN 系统架构由**分发服务系统**、**负载均衡系统**和**运营管理系统**三大部分组成，如图所示：
{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_10.jpeg%}

### 分发服务系统

{% alert success no-icon%}
:dart:该系统将内容从源中心向边缘的 CDN 服务器推送和存储；承担实际内容的数据流、全网分发工作和面向最终用户的数据请求服务
{%endalert%}

分发服务系统最基本的工作单元是众多的 CDN 服务器（缓存服务器，Cache），缓存服务器负责直接响应最终用户的访问请求，把缓存在本地的内容快速地提供给用户；同时缓存服务器还负责与源站点进行内容同步，将更新的内容以及本地没有的内容从源站点获取并保存在本地

:sparkles:一般而言，根据承载内容类型和服务种类的不同，分发服务系统会分为多个子服务系统

- 网页加速子系统
- 流媒体加速子系统
- 应用加速子系统

:notes:每个子服务系统都是一个分布式服务集群，由一群功能近似的、在地理位置上分布部署的 缓存服务器或缓存服务器集群组成，彼此间相互独立；每个子服务系统设备集群的数量根据业务发展和市场需要的不同，少则几十台，多则可达上万台，对外形成一个整体，共同承担分发服务工作

:sparkles:**Cache 设备的数量、规模、总服务能力是衡量一个 CDN 系统服务能力的最基本的指标**

{% alert info no-icon%}
:notebook:分发服务系统承担内容的更新、同步和响应用户需求的同时，还需要向上层的调度控制系统提供每个 Cache 设备的**健康状况信息、响应情况**，有时还需要提供内容分布信息，以便调度控制系统根据设定的策略决定由哪个 Cache（组）来响应用户的请求最优
{%endalert%}

### 负载均衡系统

{% alert success no-icon%}
:dart:负载均衡系统是一个 CDN 系统的神经中枢，主要功能是**负责对所有发起服务请求的用户进行访问调度，确定提供给用户的最终实际访问地址**
{%endalert%}
:sparkles:大多数 CDN 系统的负载均衡系统式分级实现的，一般而言采用两级调度系统

- 全局负载均衡（GSLB）
  - 全局负载均衡（GSLB）主要根据 **用户就近性原则**，通过对每个服务节点进行"最优"判断，确定向用户提供服务的 Cache 的物理位置
  - 最通用的 GSLB 实现方法式基于 **DNS 解析**的方式实现，也有一些采用了**应用层重定向**等方式来解决
- 本地负载均衡（SLB）
  - 本地负载均衡（SLB）主要负责节点内部的设备负载均衡，当用户请求从 GSLB 调度到 SLB 时，SLB 会根据节点内各 Cache 设备的实际能力或内容分布等因素对用户进行重定向
  - 常用的本地负载均衡方法有：基于四层负载均衡、基于七层负载均衡和链路负载均衡等

### 运营管理系统

{% alert success no-icon%}
:dart:运营管理系统管理和监控整个 CDN 集群的工作状态
{%endalert%}
:sparkles:CDN 的运营管理系统与一般的电信运营管理系统类似，分为运营管理和网络管理两个子系统

- 运营管理
  - 运营管理子系统是 CDN 系统的业务管理功能实体，负责处理业务层面的与外界系统交互所必需的一些收集、整理、交付工作，包含客户管理、产品管理、计费管理、统计分析等功能
- 网路管理
  - 网络管理子系统实现对 CDN 系统的**网络设备管理、拓扑管理、链路监控和故障管理**，为管理员提供对全网资源进行集中化管理操作的界面，通常是基于 Web 方式实现的

## CDN 部署架构

{% alert success no-icon%}
:dart:CDN 系统设计的首要目标是**尽量减少用户的访问响应时间**，为达到这一目标，CDN 系统应该尽量将用户所需要的内容存放在距离用户最近的位置。因此根据实现的功能不同，将 CDN 的部署架构再次分为两层：**CDN 边缘层**和**中心层**
{%endalert%}
`CDN 边缘层`主要负责为用户提供内容服务的 Cache 设备应部署在物理上的网络边缘位置。`CDN 中心层`主要负责全局性管理和控制的设备组成，中心层同时保存着最多的内容副本，当边缘层设备未命中时，会向中心层请求，如果在中心层仍未命中，则需要中心层向源站回源

不同 CDN 系统设计之间存在差异，中心层可能具备用户服务能力，也可能不直接提供服务，只向下级节点提供内容。如果 CDN 网络规模较大，边缘层设备直接向中心层请求内容或服务会造成中心层设备压力过大，就要考虑在边缘层和中心层之间部署一个`区域层`，负责一个区域的管理和控制，也保存部分内容副本供边缘层访问

下图是典型的 CDN 系统三级部署示意图：

{%image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_11.jpeg %}

节点是 CDN 系统中最基本的部署单元，一个 CDN 系统由大量的、地理位置上分散的 POP（point-of-presence）节点组成，为用户提供就近的内容访问服务。CDN 节点网络主要包含 CDN 骨干点 和 POP 点。

:sparkles:CDN 骨干点 和 CDN POP 点在功能上不同
{% alert success no-icon%}

- 中心和区域节点一般称为骨干点，主要作为内容分发和边缘未命中时的服务点；
- 边缘节点又被称为 POP（Point-of-Presence）节点，CDN POP 点主要作为直接向用户提供服务的节点
  {%endalert%}
  :notes:从节点构成上来说，无论是 CDN 骨干点还是 CDN POP 点，都由 Cache 设备和本地负载均衡设备构成

## Cache 设备和本地负载均衡设备

:sparkles:在一个节点中，Cache 设备和本地负载均衡设备的连接方式有两种：**旁路方式**、**穿越方式**
{%image fancybox  fig-100  centerhttps://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/others/CDN/cdn_12.jpeg%}

### 旁路方式

:sparkles:在旁路方式下，有两种 SLB`（Server Load Balancer，服务端负载均衡）` 实现方式：

- 在早期，这种 SLB 一般由软件实现。SLB 和 Cache 设备都具有公共的 IP 地址，SLB 和 Cache 构成并联关系。用户需要先访问 SLB 设备，然后再以重定向的方式访问特定的 Cache。这种实现方式简单灵活，扩展性好，缺点是安全性较差，而且需要依赖于应用层重定向
- 随着技术的发展，L4-7 交换机也可采用旁路部署（负载均衡硬件设备的部署）方式，旁挂在路由交换设备上，数据流量通过三角传输方式进行

{% alert info no-icon%}
:notebook:在 CDN 系统中，不仅分发服务系统和调度控制系统是分布式部署的，运营管理系统也是分级分布式部署的，每个节点都是运营管理数据的生成点和采集点，通过日志和网管代理等方式上报数据。**可以说，CDN 本身就是一个大型的具有中央控制能力的分布式服务系统**
{%endalert%}

### 穿越方式

在穿越方式下，SLB 一般由 L4-7 交换机实现，SLB 向外提供可访问的 **公网 IP 地址**，每台 Cache 仅分配私网 IP 地址（ `VIP `），该台 SLB 下挂的所有 Cache 构成一个服务组。所有用户请求和媒体流都经过该 SLB 设备，再由 SLB 设备进行向上向下转发
{% alert success no-icon%}
:sparkles:SLB 实际上承担了 `NAT（Network Address Translation，网络地址转换）`功能，向用户屏蔽了 Cache 设备的 IP 地址，这种方式是 CDN 系统中应用较多的方式
{%endalert%}
:+1:优点是具有较高的安全性和可靠性

:persevere:缺点是是 L4-7 交换机通常较为昂贵；当节点容量大时，L4-7 交换机容易形成性能瓶颈。

:notes:随着 `LVS (Linux Virtual Server，即Linux虚拟服务器)` 等技术的兴起，SLB 设备价格有了大幅下降

## 其他知识

### 流量劫持

{% alert success no-icon%}
其实，CDN 本身就是一种**良性的**DNS 劫持，不同于黑客强制 DNS 把域名解析到自己的钓鱼 IP 上，CDN 则是让 DNS 主动配合，把域名解析到临近的服务器上
{%endalert%}
:sparkles:劫持通常分为两类：

- 域名劫持（DNS 劫持）
  - 通常是指域名指向到非正常 IP（恶意 IP），该恶意 IP 通过反向代理的方式，在能返回网页正常内容的情况，可能插入恶意代码、监听网民访问、劫持敏感信息等操作。通常验证一个域名是否被劫持的方法是 PING 一个域名，如果发现 PING 出来的 IP 不是您的服务器真实 IP，则可以确定被劫持了（如果使用了云平台安全加速，可能此时 ping 出来的是云平台 CDN 服务器的 IP，这并不是劫持）
- 数据劫持
  - 通常由电信运营商中某些员工等勾结犯罪分子，在公网中进行数据支持，插入，此类情况极隐蔽，不会改变用户域名解析 IP，而是直接数据流经运营商宽带时在网页中挺入内容，网页使用`HTTPS`加密可以解决这一问题

如果使用 CDN 服务时，当源站向 CDN 返回被劫持的内容时，此时 CDN 将获取到的并不是正确的网页内容（而是经运营商篡改强制植入广告的页面），此时可能导致该内容在 CDN 中长时间缓存，发现这种问题，可以通过清理 CDN 缓存解决

### CDN 缓存

CDN 边缘节点缓存策略因服务商不同而不同，但一般都会遵循 http 标准协议，通过 http 响应头中的 `Cache-control: max-age`的字段来设置 CDN 边缘节点数据缓存时间

当客户端向 CDN 节点请求数据时，CDN 节点会判断缓存数据是否过期，若缓存数据并没有过期，则直接将缓存数据返回给客户端；否则，CDN 节点就会向源站发出**回源请求（back to the source request）**，从源站拉取最新数据，更新本地缓存，并将最新数据返回给客户端。

:question:如何判断数据已经过期？
{% alert success no-icon%}
CDN 服务商一般会提供基于文件后缀、目录多个维度来指定 CDN 缓存时间，为用户提供更精细化的缓存管理
{%endalert%}

CDN 缓存时间会对**回源率**产生直接的影响。若 CDN 缓存时间较短，CDN 边缘节点上的数据会经常失效，导致频繁回源，增加了源站的负载，同时也增大的访问延时；若 CDN 缓存时间太长，会带来数据更新时间慢的问题。开发者需要对特定的业务，来做特定的数据缓存时间管理

CDN 边缘节点对开发者是透明的，相比于浏览器 `Ctrl+F5` 的强制刷新来使浏览器本地缓存失效，开发者可以通过 CDN 服务商提供的“**刷新缓存**”接口来达到清理 CDN 边缘节点缓存的目的。这样开发者在更新数据后，可以使用**刷新缓存**功能来强制 CDN 节点上的数据缓存过期，保证客户端在访问时，拉取到最新的数据

### CDN 预热数据

之前讨论的 CDN 访问模式都是基于`pull`模式，由用户决策哪部分热点数据会最终留在 CDN 缓存中；对于大促销场景，往往需要预先将活动相关资源**预热**到**边缘节点**，避免大促销开启后，大量用户访问，造成源站压力过大，这时候采用的是`push`模式

## 附录

[CDN 详解——segmentFault](https://segmentfault.com/a/1190000010631404)
[最全的一次 CDN 详解——知乎](https://zhuanlan.zhihu.com/p/28940451)
[到底什么是 CDN](https://mp.weixin.qq.com/s/uA9ChKqr2q3QgYeotVNULQ)
[阿里云 CDN](https://help.aliyun.com/document_detail/27101.html?spm=5176.208361.1107621.2.7cae57e0S4SUAl)
[为了搞清楚 CDN 的原理，我头都秃了...](https://mp.weixin.qq.com/s?__biz=MzI3NzE0NjcwMg==&mid=2650167702&idx=3&sn=3e1bb77d9fe42beb67d1649ba8a8168d&chksm=f36844b7c41fcda14f1f914a5ca4c2989c80369e1aa7f0209840c3a39b8eb0c1e116a325f470&scene=27#wechat_redirect)
