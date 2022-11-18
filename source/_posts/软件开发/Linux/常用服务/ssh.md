---
title: SSH 服务
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 通用技能
tags: 操作系统
keywords: SSH
excerpt: SSH 是进行 Linux 访问时不可避免的一种操作，平时一直在使用，每次使用就依靠百度，感觉并没有提升，所以搜索相关资料，在本文总结 SSH 相关知识
date: 2022-01-07 23:41:30
thumbnailImage:
---

<!-- toc -->

## 简介

{% alert success no-icon%}

SSH（Secure SHell）协议是**应用层基础上的安全网络协议**。它是**专为远程登录会话和其他网络服务提供安全性的协议**，即使登陆过程被中途截获，密码也不会泄露。通过 SSH 协议可有效弥补网络中的漏洞。
{%endalert%}

## SSH 原理

通过分析 SSH 的原理，可以更进一步的了解 SSH 的特点，下面总结出 SSH 协议的原理

### SSH 口令登录

SSH 口令登陆的时空原理图如下：

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/ssh原理时空图.png %}

使用 SSH 进行登陆时，主要分为以下几步：
{% alert success no-icon%}

1. 用户使用`ssh user@host`命令对远程主机发起登录请求
2. 远程主机将自己的公钥返回给请求主机
3. 请求主机使用公钥对用户输入的密码进行加密
4. 请求主机将加密后的密码发送给远程主机
5. 远程主机使用私钥对密码进行解密
6. 最后，远程主机判断解密后的密码是否与用户密码一致，如果一致则同意登陆，否则拒绝登陆
   {%endalert%}

### 公钥免密登录

{% alert success no-icon%}

SSH 的另一个的特点是支持免密登陆，那如何让 SSH 免密登陆，实现**自动登陆需求**呢？
{%endalert%}
SSH 公钥免密登陆的时空原理图如下：
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/ssh免密登陆.png %}

{% alert info no-icon%}

上图就是 ssh 免密登陆原理图，从上图可以看出，SSH 免密登陆的前提是使用`ssh-keygen -t RSA` 生成公私秘钥对，然后通过 `ssh-copy-id -i ~/.ssh/id_rsa.pub user@host` 命令将公钥分发至远程主机，所有已知的公钥会存于 `.ssh/authorized_keys` 文件中
{%endalert%}

接下来的每次免密登陆步骤如下：

{% alert success no-icon%}

1. 用户使用 ssh user@host 命令对远程主机发起登陆；
2. 远程主机对用户返回一个随机串；
3. 用户所在主机使用私钥对这个随机串进行加密，并将加密的随机串返回至远程主机；
4. 远程主机使用登陆客户发送过来的公钥对加密随机串进行解密；
5. 如果解密成功，就证明用户的登陆信息是正确的，则允许登陆；否则拒绝用户登陆

{%endalert%}

### 中间人攻击

{% alert info no-icon%}
无论是口令登陆还是免密登陆都存在漏洞风险（**中间人攻击**），原因是两种方式都存在这样的一个步骤——**客户端接受服务器的公钥**。SSH 不像 https 协议那样，SSH 协议的公钥是没有证书中心（CA）公证的,这就导致如果有人截获了登陆请求，然后冒充远程主机，将伪造的公钥发给用户，那么用户很难辨别真伪，用户再通过伪造的公钥加密密码，再发送给冒充主机，此时冒充的主机就可以获取用户的登陆密码了，那么 SSH 的安全机制就荡然无存了
{%endalert%}

既然存在这种问题，SSH 就想了一个办法来绕开这个问题，也就是`.sknown_hosts`文件的作用

> The authenticity of host 'xx.xx.xx.xx(xx.xx.xx.xx)' can't be established.
> ECDSA key fingerprint is --------------------------------------.
> Are you sure you want to continue connecting (yes/no)?

上面这段话的意思是，无法确认`xx.xx.xx.xx`主机的真实性，只知道它的公钥指纹，问你还想继续连接吗？这样我们就可以看到，SSH 是将这个问题抛给了 SSH 使用者，让 SSH 使用者自己来确定是否相信远程主机。但是这样对于用户来说，就存在一个难题，用户怎么知道远程主机的公钥指纹是多少？**此时就需要远程主机必须公开自己的公钥指纹，以便用户自行核对**

在经过用户的风险衡量以后，用户输入 `yes` 来决定接受这个远程主机的公钥。紧接着，系统会出现以下这样的一句提示，表示远程主机已经得到认可：

> Warning: Permanently added 'xx.xx.xx.xx' (ECDSA) to the list of known hosts.

当远程主机的公钥被接受以后，它就会被保存在文件~/.ssh/known_hosts 之中。下次再连接这台主机，系统就会认出它的公钥已经保存在本地了，从而跳过警告部分，直接提示输入密码。

{% alert warning no-icon%}
但是由于 known_hosts 这个机制的存在，也会引起一些问题，比如：远程主机的重新装操作系统了，远程主机就会重新生成公钥，如果我们再登陆远程主机时，由于我们本地的 known_hosts 文件中记录了原来的公钥，此时就会提示指纹认证失败的错误，这个时候我们只需要删除本地的 known_hosts 文件即可。

又比如，我们经常会写一些自动化的脚本，会自动的登陆到远程主机上去，但是这个 known_hosts 机制却必须要我们手动输入 yes 才能完成远程登陆，这样整个自动化登陆就无法完成了，但是我们可以通过修改 `/etc/ssh/ssh_config` 配置文件，跳过这个 known_hosts 的询问机制，将 `# StrictHostKeyChecking ask` 修改为 `StrictHostKeyChecking no`即可
{%endalert%}

## ssh 基本用法

SSH 服务的实现分客户端 `openssh-client` 和 `openssh-server`，如果你只是想登陆别的机器的 SSH 只需要安装 openssh-client，如果要使本机开放 SSH 服务就需要安装 openssh-server
{% alert success no-icon%}
远程管理工具 ssh 具有数据加密传输、网络开销小以及应用平台范围广的特点，是远程管理中最常见的控制工具，是 SSH 协议的一种命令实现
{%endalert%}

### ssh 口令登录

假定你要以用户名 user，登录远程主机 host，只要一条简单命令就可以了

```bash
ssh user@host   # 如：ssh root@192.168.0.111

ssh host        # 如果本地用户名与远程用户名一致，登录时可以省略用户名。

ssh -p 2222 user@host # 使用p参数，可以修改这个端口
```

### ssh 免密登录配置

使用 ssh **免密登录**主要分为<font style="color:red;font-weight:bold">如下四步</font>：

<font style="color:red;font-weight:bold">1. 修改 sshd 服务的配置文件，服务端需要支持免密登陆</font>

```bash
vim /etc/ssh/sshd_config
#---------------------------------将以下几个选项取消注释
RSAAuthentication yes     ## RSA认证
PubkeyAuthentication yes  ## 公钥认证
AuthorizedKeysFile .ssh/authorized_keys  ## 公钥认证文件路径
```

{% alert info no-icon%}
由于修改了配置文件所以需要重新启动`ssh`服务，`systemctl restart sshd`
{%endalert%}

<font style="color:red;font-weight:bold">2. 客户端生成公钥和私钥</font>

```bash
# 进入到当前用户的家目录
cd ~/.ssh
# 最终会在 ~/.ssh 生成 rsa 公私钥,id_rsa id_rsa.pub 两个文件
ssh-keygen -t rsa
```

{% alert info no-icon%}
如果当前目录已经存在`id_rsa`文件，最好生成其他文件的名的公私钥匙
{%endalert%}

<font style="color:red;font-weight:bold">3. 将客户端的公钥上传到服务端</font>

```bash
ssh-copy-id user@host
```

<font style="color:red;font-weight:bold">4. 配置本地 config 文件</font>

```
## Read more about SSH config files: https://linux.die.net/man/5/ssh_config
## 文件在 ~/.ssh/config
Host hostname			#方便使用者区别机器的名字
    HostName hostname	#目的机器的IP
    User root			## ssh 登录时的用户名
    Port 22				## ssh 使用的端口，默认是22
    IdentityFile		#id_rsa.pub文件对应私钥所在的文件路径
```

### 使 ssh 远程连接速度加快

1. 修改 ssh 远程服务配置文件

```shell
vim /etc/ssh/
#------------------
GSSAPIAuthentication no
UseDNS	no
```

2. 修改 hosts 文件

```shell
vim /etc/hosts	#将连接的主机名加入到当前文件中
```

3. 重启 ssh 服务

```shell
systemctl restart sshd
```

## SSH 端口转发

使用 SSH 能够做到端口转发功能，主要分为两种：本地端口转发以及远程端口转发

### 本地端口转发

有时需要将指定数据传送到目标主机，从而形成点对点的「端口转发通信」。为了区别后文的「远程端口转发」，把这种情况称为「本地端口转发」（Local forwarding）
{% alert info no-icon%}

假定 host1 是本地主机，host2 是远程主机。由于种种原因，这两台主机之间无法连通。但是，另外还有一台 host3，可以同时连通前面两台主机。因此，很自然的想法就是，通过 host3，将 host1 连上 host2

在 host1 执行下面的命令：
`ssh -L 2121:host2:21 host3`
命令中的 L 参数一共接受三个值，分别是「**本地端口:目标主机:目标主机端口**」，它们之间用冒号分隔
这条命令的意思是：指定 SSH 绑定本地端口 2121，然后指定 host3 将所有的数据，转发到目标主机 host2 的 21 端口（假定 host2 运行 FTP，默认端口为 21）。
这样一来，只要连接 host1 的 2121 端口，就等于连上了 host2 的 21 端口
`ftp localhost:2121`
「本地端口转发」使得 host1 和 host3 之间仿佛形成一个数据传输的秘密隧道，因此又被称为「**SSH 隧道**」
{%endalert%}
{% alert warning no-icon%}

下面是一个比较有趣的例子。
`ssh -L 5900:localhost:5900 host3`
它表示将本机的 5900 端口绑定 host3 的 5900 端口（这里的 localhost 指的是 host3，因为目标主机是相对 host3 而言的）
另一个例子是通过 host3 的端口转发，ssh 登录 host2。
`ssh -L 9001:host2:22 host3`
这时，只要 ssh 登录本机的 9001 端口，就相当于登录 host2
`ssh -p 9001 localhost`
{%endalert%}

### 远程端口转发

本地端口转发是指绑定本地端口的转发，那么「远程端口转发」（remote forwarding）是指**绑定远程端口的转发**
{% alert warning no-icon%}
host1 与 host2 之间无法连通，必须借助 host3 转发。但是，特殊情况出现了，host3 是一台**内网机器**，它可以连接**外网**的 host1，但是反过来外网的 host1 连不上内网的 host3。此时「本地端口转发」就不能用了，可以使用如下方式解决：由于 host3 可以连 host1，那么就从 host3 上建立与 host1 的 SSH 连接，然后在 host1 上 使用这条连接就可以做到双方进行通信
我们在 host3 执行下面的命令：
`ssh -R 2121:host2:21 host1`
R 参数也是接受三个值，分别是「**远程主机端口:目标主机:目标主机端口**」
这条命令的意思，让 host1 监听它自己的 2121 端口，然后将所有数据经由 host3，转发到 host2 的 21 端口。由于对于 host3 来说，host1 是远程主机，所以这种情况就被称为「远程端口绑定」
绑定之后，我们在 host1 就可以连接 host2 了：
`ftp localhost:2121`
{%endalert%}
这里必须指出，「远程端口转发」的前提条件是：host1 和 host3 两台主机都有 sshd 和 ssh 客户端

## SCP 常见用法

scp 是 secure copy 的简写，用于在 Linux 下进行远程拷贝文件的命令，和它类似的命令有 cp，不过 cp 只是在本机进行拷贝不能跨服务器，而且 scp 传输是**加密的**。可能会稍微影响一下速度。两台主机之间复制文件必须同时有两台主机的复制执行帐号和操作权限

```bash
# 把远程的文件复制到本地
scp root@www.test.com:/val/test/test.tar.gz /val/test/test.tar.gz
# 把远程的目录复制到本地
scp -r root@www.test.com:/val/test/ /val/test/
# 把本地的文件复制到远程主机上
scp /val/test.tar.gz root@www.test.com:/val/test.tar.gz
# 把本地的目录复制到远程主机上
scp -r ./ubuntu_env/ root@192.168.0.111:/home/test
```

## 附录

[ssh 登录原理解析](https://blog.csdn.net/weixin_42616506/article/details/97262632)
[什么是 SSH 以及常见的 ssh 功能](https://blog.csdn.net/u013452337/article/details/80847113)
[SSH 三步解决免密登录](https://blog.csdn.net/jeikerxiao/article/details/84105529)
[MobaXterm 监控服务器的资源(CPU/RAM/Network/disk/...) 使用情况](https://www.shuzhiduo.com/A/MyJxpmqeJn/)
