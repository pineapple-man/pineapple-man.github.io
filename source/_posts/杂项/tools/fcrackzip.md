---
title: fcrackzip 工具的使用
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-02-16 12:57:56
thumbnailImage:
categories: 通用技能
tags: 杂七杂八
keywords: fcrackzip
excerpt: 最近从网上下载的一个压缩包居然需要密码才能解压，但是提供者并没有提供解压密码，所以本文主要介绍如何使用 fcrackzip 工具进行压缩包密码破解
---

<!-- toc -->

## 概述

fcrackzip 是一款专门破解 zip 类型压缩文件密码的工具，工具小巧方便、破解速度快，能使用字典和指定字符集破解，适用于 linux、mac osx 系统

## `fcrackzip` 工具的使用

本小节将主要介绍如何使用 fcrackzip 工具的使用。同样从最简单的安装开始

### `fcrackzip` 安装

对于 mac 系统的电脑可以使用如下命令安装 fcrackzip

```sh
brew install fcrackzip
```

### `fcrackzip` 命令的使用

```
终端输入命令： fcrackzip -h
显示结果：
fcrackzip version 1.0, a fast/free zip password cracker
written by Marc Lehmann <pcg@goof.com> You can find more info on
http://www.goof.com/pcg/marc/

USAGE: fcrackzip
          [-b|--brute-force]            use brute force algorithm
          [-D|--dictionary]             use a dictionary
          [-B|--benchmark]              execute a small benchmark
          [-c|--charset characterset]   use characters from charset
          [-h|--help]                   show this message
          [--version]                   show the version of this program
          [-V|--validate]               sanity-check the algortihm
          [-v|--verbose]                be more verbose
          [-p|--init-password string]   use string as initial password/file
          [-l|--length min-max]         check password with length min to max
          [-u|--use-unzip]              use unzip to weed out wrong passwords
          [-m|--method num]             use method number "num" (see below)
          [-2|--modulo r/m]             only calculcate 1/m of the password
          file...                    the zipfiles to crack
```

其中常用的参数如下：

|     常用参数      |                         含义                         |
| :---------------: | :--------------------------------------------------: |
| `-c characterset` |                 指定密码使用的字符集                 |
|       `-b`        |                表示使用暴利破解的方式                |
|   `-l min-max`    | 表示需要破解密码的长度，最短为 min 位，最长为 max 位 |
|       `-u`        |   表示只显示破解出来的密码，其他错误的密码不显示出   |

其中需要注意的是指定密码使用的字符集，常见的配置为：`-c 'aA1!'`

| 字符集参数 |               含义                |
| :--------: | :-------------------------------: |
|    `a`     |         表示小写字母[a-z]         |
|    `A`     |         表示大写字母[A-Z]         |
|    `1`     |        表示阿拉伯数字[0-9]        |
|    `!`     | 表示特殊字符[!:$%&/()=?{[]}+\*~#] |

{% alert warning no-icon %}

有一个文件 encode.zip 文件加密，密码为 `Pineapple-man123.` 就可以通过使用 fcrackzip 进行暴力破解

```
fcrackzip -b -c 'aA1!' -l 1-20 -u encode.zip
```

输入命令后静静等待就可以了，最终终端会显示出最终暴力破解出来的密码

{% endalert %}

## 附录

[mac 破解 zip 压缩文件密码](https://www.jianshu.com/p/7345247d73c0)
