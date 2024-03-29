---
title: RabbitMQ（一）环境配置
toc: true
clearReading: true
thumbnailImagePosition: bottom
metaAlignment: center
date: 2021-10-23 16:23:01
categories: 中间件
tags:
  - MQ
keywords:
  - RabbitMQ 安装
  - 消息队列
excerpt: 本文主要讲解在Centos7上进行 RabiitMQ 3.9 的环境配置
---

<!-- toc -->

## rpm 安装

在 Centos7 上进行 RabbitMQ 的环境配置，具体的步骤如下

### 下载 RabbitMQ

:book:RabbitMQ 可以在官网进行下载，[点击前往官网](https://www.rabbitmq.com/download.html)

![download](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20211016220643170.png)
复制链接地址

```bash
wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.9.7/rabbitmq-server-3.9.7-1.el7.noarch.rpm
#完成下载后将在当前目录存在 rabbitmq-server-3.9.7-1.el7.noarch.rpm
rpm -ivh rabbitmq-server-3.9.7-1.el7.noarch.rpm
#警告：rabbitmq-server-3.9.7-1.el7.noarch.rpm: 头V4 RSA/SHA512 Signature, 密钥 ID 6026dfca: NOKEY
#错误：依赖检测失败：
#        Erlang >= 23.2 被 rabbitmq-server-3.9.7-1.el7.noarch 需要
```

:notes:由于 rabbitmq 是由 Erlang 编写的，所以还需要安装 Erlang 环境，如果想看 RabbitMQ 对 Erlang 的版本依赖可以前往[此处](https://www.rabbitmq.com/which-Erlang.html)查看

### 下载 Erlang

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20211016221551023.png   %}

```bash
rpm -ivh Erlang-23.2.7-2.el7.x86_64.rpm
rpm -ivh rabbitmq-server-3.9.7-1.el7.noarch.rpm
```

:book:由于是通过**rpm 安装**，默认将 rabbitmq 软件相关命令加入到环境变量中了，所以**不需要再额外配置系统环境变量**

### 启动服务

```bash
## 启动rabbitmq
systemctl start rabbitmq-server

## 查看rabbitmq状态
systemctl status rabbitmq-server
```

服务启动之后可以通过可视化前端页面查看 RabbitMQ 服务的相关信息

> :notes:默认情况下，rabbitmq 没有安装 web 端的客户端软件，需要安装才可以生效

```bash
## 安装 RabbitMQWeb管理界面插件
rabbitmq-plugins enable rabbitmq_management
```

插件完成之后打开浏览器，访问`服务器ip:15672`就可以看到管理界面了

> `rabbitmq`有一个默认的账号密码`guest`，但该情况仅限于本机 localhost 进行访问，所以如果希望远程访问 RabbitMQ，还需要添加一个远程登录的用户

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20211016220002445.png   %}

:notes:RabbitMQ 有自己的角色管理系统，因此需要给 RabbitMQ 添加相关角色

### 角色管理

```bash
# 添加用户
rabbitmqctl add_user 用户名 密码

# 设置用户角色,分配操作权限
rabbitmqctl set_user_tags 用户名 角色

# 为用户添加资源权限(授予访问虚拟机根节点的所有权限)
# set_permissions [-p <vhostpath>] <user> <conf> <write> <read>
rabbitmqctl set_permissions -p / 用户名 ".*" ".*" ".*"
```

:sparkles:角色有四种：

- `administrator`：可以登录控制台、查看所有信息、并对 rabbitmq 进行管理
- `monToring`：监控者；登录控制台，查看所有信息
- `policymaker`：策略制定者；登录控制台指定策略
- `managment`：普通管理员；登录控制

创建完成后即可登录到 rabbitMQ 管理后台,rabbitMQ 的管理系统界面如下:

{% image fancybox  fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20211016223337511.png  %}

:sparkles:其他 rabbitmq 命令

```bash
# 删除用户
rabbitmqctl delete_user 用户名

# 修改密码
rabbitmqctl change_ password 用户名 新密码

# 查看用户清单
rabbitmqctl list_users
```

## 容器化安装

```bash
# 安装并启动rabbitmq容器
docker run -d --name myRabbitMQ -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=123456 -p 15672:15672 -p 5672:5672 rabbitmq:3.8.14-management
```

## 常见问题

### `Stats in management UI are disabled on this node`

由于容器启动之后没有进行配置，所以查看不了监控数据信息，执行下面操作即可做到远程查看监控数据

{% codeblock lang:bash %} #进入 rabbitmq 容器
docker exec -it {rabbitmq 容器名称或者 id} /bin/bash

#进入容器后，cd 到以下路径
cd /etc/rabbitmq/conf.d/

#修改 management_agent.disable_metrics_collector = false
echo management_agent.disable_metrics_collector = false > management_agent.disable_metrics_collector.conf

#退出容器
exit

#重启 rabbitmq 容器，随后重新刷新前端页面即可查看相关监控数据
docker retart {rabbitmq 容器 id}

{% endcodeblock %}

### 启动容器之后，并不能够访问前端页面

手动安装之后，需要启动插件才会有前端页面，使用容器部署时，仍然需要启动插件。
{% codeblock  lang:bash %}
docker exec -it {rabbitmq 容器名称或者 id} rabbitmq-plugins enable rabbitmq_management

#重启 rabbitmq 容器
docker retart {rabbitmq 容器 id}
{% endcodeblock %}

## 附录

[RabbitMQ 超详细安装教程（Linux）](https://blog.csdn.net/qq_45173404/article/details/116429302)

[Docker 部署 rabbitmq 遇到的问题](https://blog.csdn.net/qq_45369827/article/details/115921401)
