---
title: SSH 常用操作
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage: 
categories: 通用技能
tags: 操作系统
keywords: SSH
excerpt: SSH 是进行 Linux 访问时不可避免的一种操作，平时一直在使用，每次使用就依靠百度，感觉并没有提升，所以搜索相关资料，在本文总结 SSH 相关知识
---
# 1. 简介

SSH（Secure SHell）安全的 shell，在应用层基础上的安全网络协议。它是专为远程登录会话和其他网络服务提供安全性的协议，可有效弥补网络中的漏洞。

SSH 是一种网络协议，用于计算机之间的加密登录。如果一个用户从本地计算机，使用SSH协议登录另一台远程计算机，我们就可以认为，这种登录是安全的，即使被中途截获，密码也不会泄露。

# 2. SSH 原理



## 2.1 SSH 口令登录

原理时空图如下

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/ssh原理时空图.png)

使用 SSH 进行登陆时，主要分为以下几步：

1. 用户使用`ssh user@host`命令对远程主机发起登录请求
2. 远程主机将自己的公钥返回给请求主机
3. 请求主机使用公钥对用户输入的密码进行加密
4. 请求主机将加密后的密码发送给远程主机
5. 远程主机使用私钥对密码进行解密
6. 最后，远程主机判断解密后的密码是否与用户密码一致，一致同意登陆，否则反之

注意：
这一过程存在漏洞风险。由于SSH不像https协议那样，SSH协议的公钥是没有证书中心（CA）公证的，也就是说，都是自己签发的。这就导致如果有人截获了登陆请求，然后冒充远程主机，将伪造的公钥发给用户，那么用户很难辨别真伪，用户再通过伪造的公钥加密密码，再发送给冒充主机，此时冒充的主机就可以获取用户的登陆密码了，那么SSH的安全机制就荡然无存了，这也就是我们常说的中间人攻击。

## 公钥免密登录

总是会挂很多的自动化脚本，比如自动的登陆到一台远程主机上进行一些操作，又比如Ansible就可以配置自动登陆到远程主机，但是上面说到的SSH，都需要密码登陆，那如何让SSH免密登陆，实现我们的自动登陆需求呢？这就是这里要将的公钥免密登陆。

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/ssh免密登录.png)

上图就是ssh免密登陆原理图，从上图可以看出，SSH免密登陆的前提是使用ssh-keygen -t RSA生成公私秘钥对，然后通过ssh-copy-id -i ~/.ssh/id_rsa.pub user@host命令将公钥分发至远程主机。接下来的每次免密登陆步骤如下：

ssh-keygen -t RSA 生成密钥对
ssh-copy-id -i ~/.ssh/id_rsa.pub user@host 将公钥发送到远程主机，会存在.ssh/authorized_keys 文件中
用户使用ssh user@host命令对远程主机发起登陆；
远程主机对用户返回一个随机串；
用户所在主机使用私钥对这个随机串进行加密，并将加密的随机串返回至远程主机；
远程主机使用分发过来的公钥对加密随机串进行解密；
如果解密成功，就证明用户的登陆信息是正确的，则允许登陆；否则反之。

## know_hosts文件的作用

上面这段话的意思是，无法确认49.234.102.232主机的真实性，只知道它的公钥指纹，问你还想继续连接吗？这样我们就可以看到，SSH是将这个问题抛给了SSH使用者，让SSH使用者自己来确定是否相信远程主机。但是这样对于用户来说，就存在一个难题，用户怎么知道远程主机的公钥指纹是多少；这的确是一个问题，此时就需要远程主机必须公开自己的公钥指纹，以便用户自行核对。

在经过用户的风险衡量以后，用户只需要输入yes来决定接受这个远程主机的公钥。紧接着，系统会出现以下这样的一句提示，表示远程主机已经得到认可：
Warning: Permanently added '49.234.102.232' (ECDSA) to the list of known hosts.

当远程主机的公钥被接受以后，它就会被保存在文件~/.ssh/known_hosts之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。

但是由于known_hosts这个机制的存在，也会引起一些问题，比如远程主机的重新装操作系统了，远程主机就会重新生成公钥，如果我们再登陆远程主机时，由于我们本地的known_hosts文件中记录了原来的公钥，此时就会提示指纹认证失败的错误，这个时候我们只需要删除本地的known_hosts文件即可。又比如，我们经常会写一些自动化的脚本，会自动的登陆到远程主机上去，但是这个known_hosts机制却必须要我们手动输入yes才能完成远程登陆，这样整个自动化登陆就无法完成了，但是我们可以通过修改/etc/ssh/ssh_config配置文件，跳过这个known_hosts的询问机制，将# StrictHostKeyChecking ask修改为StrictHostKeyChecking no即可。

## 使SSH远程连接速度加快

1. 修改ssh远程服务配置文件

```shell
vim /etc/ssh/
#------------------
GSSAPIAuthentication no
UseDNS	no
```

2. 修改hosts文件

```shell
vim /etc/hosts	#将连接的主机名加入到当前文件中
```

3. 重启ssh服务

```shell
systemctl restart sshd
```



# mobaxterm远程服务器监视数据

https://www.shuzhiduo.com/A/MyJxpmqeJn/





# 免密登录

使用 ssh **免密登录**主要分为<font style="color:red;font-weight:bold">如下四步</font>

## 1 修改 sshd 服务的配置文件

```shell
vim /etc/ssh/sshd_config
#---------------------------------将以下几个选项取消注释
RSAAuthentication yes     # RSA认证
PubkeyAuthentication yes  # 公钥认证
AuthorizedKeysFile .ssh/authorized_keys  # 公钥认证文件路径
```

:notes:由于修改了配置文件所以需要重新启动`ssh`服务

```shell
systemctl restart sshd
```

## 2 本地机器上生成公钥和私钥

```shell
# 进入到当前用户的家目录
cd ~/.ssh
# 生成 rsa 公私钥
ssh-keygen -t rsa
```

:notes:如果当前目录已经存在`id_rsa`文件，最好生成其他文件的名的公私钥匙

## 3 将公钥加入到服务器中

```shell
# 将上一步生成的公钥加入到服务器即可
scp ~/.ssh/id_rsa.pub root@master:~/.ssh/
```

:notes:如果远程服务器并不存在对应的目录，则需要现在服务器上创建对应的目录

```shell
# 在远程服务器上创建对应的目录
mkdir -p ~/.ssh
# 创建ssh认证公钥文件
touch authorized_keys
# 将本地的公钥加入到认证公钥文件中
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

## 4 配置本地 config 文件

```shell
# Read more about SSH config files: https://linux.die.net/man/5/ssh_config
# 文件在 ~/.ssh/config
Host hostname			#方便使用者区别机器的名字
    HostName hostname	#目的机器的IP
    User root			# ssh 登录时的用户名
    Port 22				# ssh 使用的端口，默认是22
    IdentityFile		#id_rsa.pub文件对应私钥所在的文件路径
```





# 附录

https://blog.csdn.net/weixin_42616506/article/details/97262632

https://blog.csdn.net/u013452337/article/details/80847113

https://blog.csdn.net/jeikerxiao/article/details/84105529

https://blog.csdn.net/pipisorry/article/details/52269785

https://blog.csdn.net/li528405176/article/details/82810342

https://blog.csdn.net/weixin_41476978/article/details/79913158

