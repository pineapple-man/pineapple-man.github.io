---
title: Java读取配置
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: Java
tags: Java IO
keywords: 如何让一个程序能够通过配置文件进行灵活启动
excerpt: 本文主要介绍如何在 Java 中读取配置文件
---
<!-- toc -->
## 序列化流

## properties 文件

:notes:properties文件书写注意事项：

1. 文件内容以键值对形式存在
2. 等号两边不要加空格
3. 直接写出键对应的值，不要加引号

## getResourceAsStream 方法

:sparkles:`getResourceAsStream()`方法特点

- `getResourceAsStream()`函数寻找文件的起点是 JAVA 项目编译之后的根目录
- 通常就是 **idea** 中的源根，如果是 Maven 项目，配置文件应该放在`resources`目录下

:notes:类加载器默认使用的路径是<font style="color:red;font-weight:bold">classPath下的路径</font>，使用此方法获取文件的时候不能在路径前面

{% alert success no-icon %}

getResourceAsStream 的三种方式

{% endalert %}

```java
Class.getResourceAsStream(String path);
Class.getClassLoader.getResourceAsStream(String path);
ServletContext. getResourceAsStream(String path);
```

1. Class.getResourceAsStream(String path):
   - path 不以 `/` 开头时默认是<font style="color:red;font-weight:bold">从此类所在的包下取资源</font>
   - path 以 `/` 开头，从 ClassPath 根下获取；其只是通过path构造一个绝对路径，最终还是由ClassLoader获取资源。
2. Class.getClassLoader.getResourceAsStream(String path)
   - 默认从Class文件的根目录（源目录或资源目录），<font style="color:red;font-weight:bold"> path 不能以`/`开头</font>，最终是由ClassLoader获取资源
3. ServletContext. getResourceAsStream(String path)
   - 默认从 WebAPP 根目录下取资源，Tomcat 下 path 是否以`/`开头无所谓

## 附录

https://www.cnblogs.com/lebo0425/p/6607804.html
https://www.cnblogs.com/blogtech/p/11151780.html
https://www.runoob.com/java/java-enumeration-interface.html