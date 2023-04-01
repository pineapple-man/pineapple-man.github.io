---
title: mysql 版本升级
toc: true
clearReading: true
thumbnailImage: "https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/mysql/mysql.jpg"
thumbnailImagePosition: right
metaAlignment: center
categories: 数据库
tags: MySQL
keywords: mysql
excerpt: 使用了一段时间的 MySQL 5.5，但是新的项目需要使用 MySQL 5.7 所以就将 Win10 平台上的 MySQL5.5 进行升级
date: 2022-01-10 13:09:18
---

<!-- toc -->

:boat:升级步骤主要分为：卸载原有服务、安装新的服务和新服务配置三步

## 关闭现有 mysql 服务

打开`windows`服务列表，找到现有的`MySQL`服务，对齐进行关闭

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/mysql/mysql-关闭服务.png %}

## 卸载原有服务

管理员身份打开 cmd 窗口，进入到 mysql 目录下面，将 mysql 服务移除

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/mysql/mysql-卸载原有服务.png %}

## 准备好 mysql5.7 压缩包

:books:[mysql 下载地址](https://dev.mysql.com/downloads/mysql/)

将下载好的`mysql 5.7`软件解压

## Mysql 5.7 配置

将之前 mysql 的 my.ini 文件拷贝至 mysql5.7 下，将 mysql.ini 文件配置做以下修改

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/mysql/mysql-mysqliniconfig.png %}

{% alert info no-icon %}

:notes: 版本 5.5 的 my.ini 配置中 innodb_additional_mem_pool_size，table_cache 在版本 5.7 下面已经不存在了

{% endalert %}

## 添加 mysql5.7 服务

执行 mysqld --install mysql5.7 ，将 mysql5.7 的服务添加到 win 的服务队列中

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/mysql/mysql-addservice.png %}

## 执行安装

执行安装升级命令 mysqld install，再执行 mysqld --initialize --console

{% alert info no-icon %}

:notes:如果出现下列问题，最简单的方法就是将`data`目录中的内容清空

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/mysql/mysql-install.png %}

## 修改初始密码

执行成功后会给出一个 mysql5.7 的初始的随机密码，将密码设置成自定义密码

:sparkles:为了提高安全性 mysql5.7 中 user 表的 password 字段已被取消，取而代之的事 authentication_string 字段，当然我们更改用户密码也不可以用原来的修改 user 表来实现了。下面简绍几种 mysql5.7 下修改 root 密码的方法（其他用户也大同小异）

```sql
# 方法一
update mysql.user set authentication_string=password('123456') where user='root' and Host = 'localhost';
# 方法二
alter user 'root'@'localhost' identified by '123456';
# 方法三
set password for 'root'@'localhost'=password('123456');
```

:notes:不论使用任何方法修改完密码之后，都需要将结果刷新到存储中

```sql
# 执行刷新操作
flush privileges;
```

## 启动服务

在服务列表中找到`MySQL`进行启动

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/mysql/mysql-startservice.png %}

## 升级 mysql

升级`mysql`提供`jdbc`相关操作`mysql_upgrade -uroot -p123456`

## 升级成功后，再次重启 mysql 5.7

重启服务之后，升级安装正式成功，可以正常使用`mysql 5.7`

## 附录

[MySQL5.5 升级到 5.7](https://cloud.tencent.com/developer/article/1621566?from=article.detail.1671033)
[CSDN 推荐方法](https://blog.csdn.net/qq_33472557/article/details/77726094)
