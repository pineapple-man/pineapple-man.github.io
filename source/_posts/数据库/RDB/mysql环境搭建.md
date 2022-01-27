---
title: MySQL(一)环境搭建
toc: true
clearReading: true
thumbnailImage: 'https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/mysql.jpg'
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: Database
keywords: mysql
excerpt: 本文主要记录 Mysql 的环境搭建
date: 2022-01-27 15:11:46
---
<!-- toc -->

Mysql 是目前常用的免费的关系性数据库，所以本系列将主要记录 MySQL 学习过程中遇到的问题
## Mysql安装(Windows为主)

针对不同系统，mysql软件安装方法也不同，主要分为通常使用的Windows安装和Ubuntu安装

### Windows 下Mysql安装

如果系统一开始安装过MySQL则需要先进行卸载

#### 安装前卸载

:notes: 如果之前安装过MySQL软件，需要重装就要先卸载

{% alert success no-icon %}

1. 清除安装残余文件
2. 清除数据残余文件(卸载前最好做到数据备份)
3. 清理注册表

{% endalert %}	
```mysql
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\Eventlog\Application\MySQL服务 目录删除
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\MySQL服务 目录删除
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet002\Services\Eventlog\Application\MySQL服务 目录删除
HKEY_LOCAL_MACHINE\SYSTEM\ControlSet002\Services\MySQL服务 目录删除
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Eventlog\Application\MySQL服务目录删除
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\MySQL服务删除
注册表中的ControlSet001,ControlSet002,不一定是001和002,可能是ControlSet005、006之类
```

#### 安装
{% alert warning no-icon %}

1. 点击MySQL数据软件，无脑下一步，直到进入如下页面

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/windows-install.png %}
{% alert success no-icon %}

- Typical：表示一般常用的组件都会被安装，默认情况下安装到 `C:\Program Files\MySQL\MySQL Server 5.5\`下
- Complete：表示会安装所有的组件。此套件会占用比较大的磁盘空间
- Custom：表示用户可以选择要安装的组件，可以更改默认按照的路径，这种按照类型最灵活，适用于高级用户

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/windows-install-2.png %}

单击 `Finish` 按钮完成安装过程。如果想马上配置数据库连接，选择 `Launch the MySQL Instance Configuration Wizard` 复选框。如果现在没有配置，以后想要配置或重新配置都可以在 `MySQL Server` 的安装目录的bin目录下（例如：`D:\ProgramFiles\MySQL5.5\MySQL Server 5.5\bin`）找到 `MySQLInstanceConfig.exe` 打开 `MySQL Instance Configuration Wizard` 向导。

#### MySQL配置
{% alert warning no-icon %}

1. MySQLInstanceConfig.exe

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/windows-config.png %}

{% alert warning no-icon %}

2. 选择配置类型

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/windows-config-type.png %}

选择配置方式，`Detailed Configuration（手动精确配置）`、`Standard Configuration（标准配置）`，我们选择`Detailed Configuration`，方便熟悉配置过程。
{% alert warning no-icon %}

3. 选择MySQL的应用模式

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/windows-server-type.png %}
{% alert success no-icon %}

- Develop Machine（开发机），使用最小数量的内存
- Server Machine（服务器），使用中等大小的内存
- Dedicated MySQL Server Machine（专用服务器），使用当前可用的最大内存

{% endalert %}

{% alert warning no-icon %}

4. 选择数据库用途选择界面

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/windows-usage.png %}

{% alert success no-icon %}

选择mysql数据库的大致用途：
- Multifunctional Database（通用多功能型，好）：此选项对事务性存储引擎（InnoDB）和非事务性（MyISAM）存储引擎的存取速度都很快。
- Transactional Database Only（服务器类型，专注于事务处理，一般）：此选项主要优化了事务性存储引擎（InnoDB），但是非事务性（MyISAM）存储引擎也能用。
- Non-Transactional Database Only（非事务处理型，较简单）主要做一些监控、记数用，对 MyISAM 数据类型的支持仅限于non-transactional，注意事务性存储引擎（InnoDB）不能用

{% endalert %}

{% alert warning no-icon %}

5. 配置InnoDB数据文件目录

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/windows-config-innodb.png %}

InnoDB的数据文件会在数据库第一次启动的时候创建，默认会创建在MySQL的安装目录下，用户可以根据实际的空间状况进行路径的选择。
{% alert warning no-icon %}

6. 并发连接设置
7. 网络选项设置
8. 选择字符集

{% endalert %}

{% alert info no-icon %}

:notes: 如果要用原来数据库的数据，最好能确定原来数据库用的是什么编码，如果这里设置的编码和原来数据库数据的编码不一致，在使用的时候可能会出现乱码。

{% endalert %}

这个比较重要，就是对mysql默认数据库语言编码进行设置，第一个是西文编码，第二个是多字节的通用utf8编码，第三个，手工选择字符集。

:notes: 如果安装时选择了字符集和 `utf8`，通过命令行客户端来操作数据库时，有时候会出现乱码，这是因为命令行客户端默认是 GBK 字符集，因此客户端与服务器端就出现了不一致的情况，会出现乱码，可以在 mysql 客户端执行下列命令解决
{% alert warning no-icon %}
```mysql
mysql> set names gbk;  
```
{% endalert %}

可以通过以下命令查看：
```mysql
mysql> show variables like 'character_set_%';
```
{% alert warning no-icon %}

9. 安全选择

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/windows-config-security.png %}

{% alert warning no-icon %}

10. 设置密码

{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/windows-config-password.png %}

### Ubuntu 下Mysql安装

这里仅介绍简单的通过软件包安装

```shell
dpkg -l |grep mysql			#检查MySQL是否已经安装
apt install mysql-server	#下载MySQL软件
netstat -tap | grep mysql	#检查MySQL是否已经启动
```
{% alert info no-icon %}

- Ubuntu 详细安装步骤可以访问[这个链接](https://www.cnblogs.com/opsprobe/p/9126864.html)
- Centos7 详细安装步骤可以访问[这个链接](https://blog.csdn.net/weixin_39854730/article/details/113398812)
{% endalert %}


## Mysql服务启动

通过软件安装之后，此时并不能直接使用 mysql ，需要将下载的软件 「 打开 」，也就是要将下载的 mysql 服务启动才能够让我们使用

### Windows 下服务启动

```powershell
net start mysql
net stop mysql
```

### 登陆 MySQL

```shell
mysql -h localhost -uroot -p	## 连接Mysql
mysql>	\q	#退出Mysql
```

## SQL概述

结构化查询语言（ Structure Query Language ）：专门用来与数据库通信的语言，SQL语言可以分为如下几类：DDL,DML,DCL,DQL，具体各种语言如何书写，博客都会记录。

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/mysql/SQL分类.png %}

{% alert success no-icon %}

- DDL:数据定义语言，用来定义数据库对象：库、表、列等
- <font style="color:red;font-weight:bold">DML：数据操作语言，用来定义数据库记录(数据)</font>
- DCL：数据控制语言，用来定义访问权限和安全级别
- <font style="color:red;font-weight:bold">DQL：数据查询语言，用来查询记录</font>

{% endalert %} 