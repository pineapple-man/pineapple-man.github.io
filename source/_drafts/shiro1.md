---
title: shiro概述
toc: true
clearReading: true
thumbnailImagePosition: bottom
metaAlignment: center
thumbnailImage: 
categories: 
  - Java
tags: 安全
keywords: Shiro
excerpt: 本文是 Shiro 的概述篇
---

https://gitee.com/mingchaohu/blog-image/raw/master/image/apache-shiro-logo.png
# Shiro 简介

## Shiro 的提问

:thinking:为什么需要使用 Shiro ？

:thinking:只能使用 Shiro 吗？

:thinking:什么是 shiro ？

Apache Shiro 是 Java 的一个安全（权限）框架

:sparkles:Shiro 有什么特点

Shiro 可以非常容易的开发出足够好的应用，其不仅可以用在`JavaSE`环境，也可以用在`JavaEE`环境，Shiro 可以完成：认证、授权、加密、会话管理、Web继承、缓存等

## Shiro 功能架构图



###  基本功能图

#### Primary Concerns

|     功能名      |                             功能                             |
| :-------------: | :----------------------------------------------------------: |
| Authentication  |          身份认证/登录，验证用户是否拥有相应的身份           |
|  Authorization  | 授权，即权限认证，验证某个已认证的用户是否拥有某个权限；即判断用户是否能进行什么操作，如：验证某个用户是否拥有某个角色，或者细粒度的验证某个用户对某个资源是否具有某个权限 |
| Session Manager | 会话管理，即用户登陆后就是就是一次会话，在没有退出之前，它的所有信息都在会话中；会话可以是普通 JavaSE 环境，也可以是 Web 环境 |
|  Cryptography   | 加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储 |



#### Supporting Features

|   功能名    |                             功能                             |
| :---------: | :----------------------------------------------------------: |
| Web Support |           Web 支持，可以非常容易的集成到 Web 环境            |
|   Caching   | 缓存，用户登录后，其用户信息、拥有的角色/权限不必每次去查，这样可以提高效率 |
| Concurrency | Shiro 支持多线程应用的并发验证，即在一个线程中开启另一个线程，能将权限自动复制过去 |
|   Testing   |                         提供测试支持                         |
|   Run As    |          允许一个用户假装为另一个用户的身份进行访问          |
| Remember Me |        记住我，即一次登录后，下次再次访问就不需要登录        |

### Shiro 外部架构

从应用程序角度观察 Shiro 完成工作的流程

|      模块名      |                             功能                             |
| :--------------: | :----------------------------------------------------------: |
|     Subject      | 应用代码直接交互的对象是 Subject，也就是说 Shiro 的对外API 核心就是 Subject，Subject 代表了当前”用户“，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是 Subject，如网络爬虫，机器人等；与 Subject 的所有交互都会委托给 Security Manager；Subject其实是一个门面，SecurityManager才是实际的执行者 |
| Security Manager | 安全管理器，所有与安全有关的操作都会与 SecurityManager交互；且其管理着所有Subject；可以看出它是 Shiro 核心，它负责与 Shiro 的其他组件进行交互，它相当于 SpringMVC 中 DispatcherServlet的角色 |
|      Realm       | Shiro 从 Realm获取安全数据（如用户、角色、权限），就是说SecurityManager要验证用户角色，那么它需要从Realm获取相应的用户进行比较以确定用户身份是否合法；也需要从 Realm得到用户相应的角色/权限进行验证用户是否能进行操作；可以把 realm看成DataSource |

资料

https://zhuanlan.zhihu.com/p/54176956

