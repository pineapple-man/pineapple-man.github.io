---
title: RBAC
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: 解决方案
tags: 安全
keywords: RBAC
excerpt: RBAC是企业常用的权限管理方案，本文主要讲解RBAC
---
## 概述
RBAC（全称：Role-Based Access Control）基于角色的权限访问控制，作为传统访问控制（自主访问，强制访 问）的有前景的代替受到广泛的关注。在RBAC中，权限与角色相关联，用户通过成为适当角色的成员而得到这些 角色的权限。这就极大地简化了权限的管理。在一个组织中，角色是为了完成各种工作而创造，用户则依据它的责 任和资格来被指派相应的角色，用户可以很容易地从一个角色被指派到另一个角色。角色可依新的需求和系统的合 并而赋予新的权限，而权限也可根据需要而从某角色中回收。角色与角色的关系可以建立起来以囊括更广泛的客观 情况。

访问控制是针对越权使用资源的防御措施，目的是为了限制访问主体（如用户等） 对访问客体（如数据库资源等） 的访问权限。企业环境中的访问控制策略大部分都采用基于角色的访问控制（RBAC）模型，是目前公认的解决大 型企业的统一资源访问控制的有效方法

## RBAC 的设计思路

基于角色的访问控制基本原理是在用户和访问权限之间加入角色这一层，实现用户和权限的分离，用户只有通过激 活角色才能获得访问权限。通过角色对权限分组，大大简化了用户权限分配表，间接地实现了对用户的分组，提高 了权限的分配效率。且加入角色层后，访问控制机制更接近真实世界中的职业分配，便于权限管理。

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20211031194436350.png)

在RBAC模型中，角色是系统根据管理中相对稳定的职权和责任来划分，每种角色可以完成一定的职能。用户通过 饰演不同的角色获得角色所拥有的权限，一旦某个用户成为某角色的成员，则此用户可以完成该角色所具有的职 能。通过将权限指定给角色而不是用户，在权限分派上提供了极大的灵活性和极细的权限指定粒度。