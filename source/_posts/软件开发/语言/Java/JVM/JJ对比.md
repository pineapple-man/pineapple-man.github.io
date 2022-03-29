---
title: JDK 和 JRE之间的差异
toc: true
clearReading: true
thumbnailImagePosition: bottom
metaAlignment: center
categories: 
- Java
tags: JVM
keywords:
  - JDK
  - JRE
excerpt: JDK和JRE 在平时学习 Java 的时候都听说过，但是一直不清不楚，所以本文主要理清楚两者之间的区别
date: 2021-10-28 23:45:23
thumbnailImage: https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/JDK.png
---
<!-- toc -->

## 概述

:question:什么是JDK？
{% alert success no-icon%}
Java Development Kit (JDK ) 是用于开发 Java 应用程序和小程序的软件开发环境，它包括 Java 运行时环境（JRE）、解释器/加载器（Java）、编译器（Javac)、存档器（jar）、文档生成器（Javadoc）以及 Java 开发所需的其他工具
{% endalert %}

:question:什么是JRE？
{% alert success no-icon%}
JRE 表示 Java运行时环境，**保证 Java 代码能够运行的最低要求**，它由Java 虚拟机 (JVM)、核心类和支持文件组成
{% endalert %}

两者之间关系如下图所示：
![jj](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/JDK.png)

:notes:JDK（Java Development Kit）是一个提供Java程序开发和执行（运行）环境的工具包（或包），主要进行两件事

- 提供开发 Java 程序的环境
- 执行Java程序

## Java 语言体系

下图是整个Jvav 语言的体系结构：

{% image fancybox fig-100 center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210328003808395.png %}

## 附录

[Differences between JDK, JRE and JVM](https://www.geeksforgeeks.org/differences-jdk-jre-jvm/?ref=lbp)
