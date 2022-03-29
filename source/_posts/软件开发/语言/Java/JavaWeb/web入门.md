---
title: Java Web 与 Tomcat 入门
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2021-10-24 00:38:47
thumbnailImage: https://www.apache.org/foundation/press/kit/poweredBy/pb-tomcat.jpg
categories: 
  - Java
tags: Tomcat
keywords:
    - Tomcat
    - JavaWeb
excerpt: 想要知道什么是Tomcat?这事还要从Java Web 聊起
---
<!-- toc -->

## JavaWeb

:question:什么是 JavaWeb？

- JavaWeb 也是使用 **Java 开发的一种程序**，不过这种程序**可以让用户通过浏览器进行访问**

:question:JavaWeb 是怎么开发出来的？

- JavaWeb 通过**请求和响应**方式进行编码

:question:什么是请求与响应?

- 请求（Request）是指：客户端给服务器发送数据
- 响应（Response）是指：服务器给客户端回传数据

:notes:请求和响应是成对出现的， 有请求就有响应

![tomca1](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/请求与响应关系.png)

:smiley:现在你可以将 Tomcat 简单的理解为 JavaWeb 的服务器，用于管理我们写的 JavaWeb 代码，并根据用户的请求，做出特定的响应

## Web 资源的分类

web 资源按**实现的技术和呈现的效果**的不同， 又分为静态资源和动态资源两种、如此处理也会方便项目的前后端分离

![tomcat2](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/Web资源分类.png)

## 常用的 Web 服务器

| Web服务器 |                           主要特点                           |
| :-------: | :----------------------------------------------------------: |
|  Tomcat   | 对 **jsp 和 Servlet 的支持**<br />**轻量级**的 javaWeb 容器（服务器） |
|   Jboss   | 遵从 JavaEE 规范的<br /> 开放源代码<br />纯 Java 的 EJB 服务器 |
| GlassFish |      强健的商业服务器， 达到产品级质量（**应用很少**）       |
|   Resin   | 对 servlet 和 JSP 提供了良好的支持<br/>性能也比较优良（**收费， 应用比较多**） |
| WebLogic  | 是目前**应用最广泛的 Web 服务器**<br />**支持 JavaEE 规范**<br/> **适合大型项目**（**收费， 用的不多， 适合大公司**） |

> :notes:这些服务器均表示**服务器上的Web应用**,并且这些都是 Java 开发人员眼中的服务器

## Tomcat服务器和Servlet版本对应关系

| Tomcat版本 | Servlet/Jsp版本 | JavaEE版本 | 运行环境 |
| :--------: | --------------- | ---------- | -------- |
|    4.1     | 2.3/1.2         | 1.3        | JDK 1.3  |
|    5.0     | 2.4/2.0         | 1.4        | JDK 1.4  |
|  5.5/6.0   | 2.5/2.1         | 5.0        | JDK 5.0  |
|    7.0     | 3.0/2.2         | 6.0        | JDK 6.0  |
|    8.0     | 3.1/2.3         | 7.0        | JDK 7.0  |

> :notes:Servlet 当前使用情况
>
> - Servlet 程序从 2.5 版本是现在使用最多的版本（**xml 配置**）
> - Servlet3.0 之后,以注解使用为核心
>
> :notes:目前企业常用版本 7.\*, 8.\*

## Tomcat 的使用

:notes:Tocat默认绑定端口为8080

### 目录介绍

|  目录   |                             作用                             |
| :-----: | :----------------------------------------------------------: |
|   bin   |              存放 Tomcat 服务器的**可执行程序**              |
|  conf   |               存放 Tocmat 服务器的**配置文件**               |
|   lib   |               存放 Tomcat 服务器的 **jar 包**                |
|  logs   |          存放 Tomcat 服务器运行时输出的**日志信息**          |
|  temp   |            存放 Tomcdat 运行时产生的**临时数据**             |
| webapps |                   存放**部署的 Web 工程**                    |
|  work   | 是 Tomcat 工作时的目录，**存放 Tomcat 运行时 jsp 翻译为 Servlet 的源码**， 和 **Session 钝化的目录** |

### Tomcat 操作

#### Tomcat 生命周期管理

:sailboat:启动Tomca的步骤如下

1. 将路径切换到tomcat的bin目录下
2. 使用启动命令启动

```shell
catalina run 
startup.bat  
```

3. 通过浏览器访问`http://localhost:8080`，进行检查，是否启动成功
4. 停止服务器

```bash
ctl+c #快速挂起进程
shutdown.bat #通过脚本停止Tomcat服务器
```

:notes:Windows 下双击 `startup.bat` 文件，小黑窗口一闪而过，这说明 Tomcat 启动失败，失败的原因可能是没有配置好 JAVA_HOME 环境变量

:older_man:常见的 JAVA_HOME 配置错误有以下几种情况：

1. JAVA_HOME 必须全大写。
2. JAVA_HOME 中间必须是下划线， 不是减号-
3. JAVA_HOME 配置的路径只需要配置到 jdk 的安装目录即可。 不需要带上 bin 目录  

#### 修改默认端口

:sailboat:修改 Tomcat 默认端口号步骤

1. 进入 Tomcat 目录下的 conf 目录
2. 打开 `server.xml`配置文件
3. 找到 `<Connector>`字段，对`port`属性进行修改

```xml
<Connector port="8081" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```

4. 修改完成，重启 Tomcat 服务器，检查修改操作是否成功

:notes:修改 port 属性可以更改端口号，端口号范围应在(1~ 65535)

### 部署 web 工程

项目完成之后，需要将对应的资源迁移到 Tomcat 服务器之上，供用户访问

#### 直接部署

将web工程的目录拷贝到Tomcat的webapp目录下，用户即可访问新增加的应用

```
http://ip:port/工程名/目录名/文件名
```

#### 修改配置文件

找到 Tomcat 安装目录下的 conf 目录`\Catalina\localhost\` 下,创建abc.xml如下的配置文件：  

```xml
<Context path="/abc" docBase="E:\book" />
```

- `Context` 表示一个工程上下文

- `path` 表示工程的访问路径:/abc

- `docBase` 表示工程目录在哪里​

:notes:配置文件名称需要和path中的属性名称相同

### Tomcat 默认页面

在浏览器地址栏中输入：`http://ip:port/`

- 没有**工程名**， 默认访问的是 **ROOT 工程**

在浏览器地址栏中输入：`http://ip:port/工程名/`

- 没有**资源名**， 默认访问 **index.html** 页面  
