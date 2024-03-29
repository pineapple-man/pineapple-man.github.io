---
title: top 命令信息查看
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2021-10-25 14:35:52
thumbnailImage:
categories: 操作系统
tags: 系统监控
keywords: top
excerpt: 本文主要讲解top命令的使用以及信息查看
---

<!-- toc -->

top 命令是 Linux 下常用的系统性能监控工具，能够实时显示系统中各个进程的资源占用状况，属于 Linux 系统下的任务管理器,并且 top 命令是一个动态显示过程，可以自动或者通过用户按键来不断刷新当前状态

## 语法

```bash
top -hv|-bcHiOSs -d secs -n max -u|U user -p pid -o fld -w [cols]
```

常用参数选项介绍：
| options | 含义 |
| :-------: | :----------------: |
| `-b` | 以批处理模式操作 |
| `-c` | 显示完整的命令 |
| `-d secs` | 屏幕刷新间隔时间 |
| `-I` | 忽略失效过程 |
| `-s` | 保密模式 |
| `-S` | 累计模式 |
| `-u user` | 指定用户名 |
| `-p pid` | 指定进程 |
| `-n max` | 指定循环显示的次数 |

## 输出结果信息描述

想要监控系统信息首先一点就是要能够看懂`top`命令提供给我们的信息，输入`top`命令后展示的信息类似于下图：

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/20211025111656.png)

top 展示的信息分为两个部分，第一部分是**系统综合数据统计**，第二部分是**详细进程列表**

### 第一部分

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20211025112754142.png)

#### 系统任务队列信息展示

第一行主要展示任务队列信息，同`uptime`命令的执行结果

```
top - 11:22:37 up 191 days,  1:36,  2 users,  load average: 1.07, 1.45, 1.52
```

|             展示信息             |                                          含义                                           |
| :------------------------------: | :-------------------------------------------------------------------------------------: |
|            `11:22:37`            |                                      当前系统时间                                       |
|       `up 191 days, 1:36`        |                            系统运行时间，格式为：天，时：分                             |
|            `2 users`             |                                     当前登录用户数                                      |
| `load average: 1.07, 1.45, 1.52` | 系统负载，即任务队列的平均长度，三个数据分别为：1 分钟、5 分钟、15 分钟前到现在的平均值 |

#### 系统进程信息展示

第二行主要展示系统中进程信息

```
Tasks: 539 total,   2 running, 442 sleeping,   0 stopped,   2 zombie
```

|    展示信息    |          含义           |
| :------------: | :---------------------: |
|  `539 total`   |   系统存在 539 个进程   |
|  `2 running`   | 正在运行的进程数是 2 个 |
| `442 sleeping` |  睡眠的进程数是 442 个  |
|  `0 stopped`   |   停止的进程数是 0 个   |
|   `2 zombie`   |    僵尸进程数是 2 个    |

#### 系统 CPU 占用率信息展示

第三行主要展示 CPU 的信息

```
%Cpu(s):  2.3 us,  1.1 sy,  0.0 ni, 95.9 id,  0.5 wa,  0.0 hi,  0.1 si,  0.0 st
```

| 展示信息  |                      含义                      |
| :-------: | :--------------------------------------------: |
| `2.3 us`  |           用户空间占用 CPU 的百分比            |
| `1.1 sy`  |           内核空间占用 CPU 的百分比            |
| `0.0 ni`  |      改变过优先级的进程占用 CPU 的百分比       |
| `95.9 id` |                空闲 CPU 百分比                 |
| `0.5 wa`  |            IO 等待占用 CPU 的百分比            |
| `0.0 hi`  |    硬中断（Hardware IRQ）占用 CPU 的百分比     |
| `0.1 si`  | 软中断（Software Interrupts）占用 CPU 的百分比 |
| `0.0 st`  |                虚拟机占用百分比                |

#### 系统内存信息展示

第四行为系统中的内存信息展示

```
KiB Mem : 64918328 total,   830476 free,  6139916 used, 57947936 buff/cache
```

|       展示信息        |         含义         |
| :-------------------: | :------------------: |
|   `64918328 total`    |     物理内存总量     |
|     `830476 free`     |     空闲内存总量     |
|    `6139916 used`     |  使用的物理内存总量  |
| `57947936 buff/cache` | 用作内核缓存的内存量 |

:notes:第四行中使用中的内存总量（used）指的是现在系统内核控制的内存数，纳入内核管理的内存不见得都在使用中，还包括过去使用过的现在可以被重复利用的内存，内核并不把这些可被重新使用的内存交还到 free 中去，因此**linux 上 free 内存会越来越少**

#### 系统内交换区信息展示

最后一行是系统中交换分区的信息展示

```
KiB Swap:        0 total,        0 free,        0 used. 57889476 avail Mem
```

|       展示信息       |                                                                   含义                                                                   |
| :------------------: | :--------------------------------------------------------------------------------------------------------------------------------------: |
|      `0 total`       |                                                                交换区总量                                                                |
|       `0 free`       |                                                              空闲交换区总量                                                              |
|       `0 used`       |                                                             使用的交换区总量                                                             |
| `57889476 avail Mem` | 缓冲的交换区总量，内存中的内容被换出到交换区，而后又被换入到内存，但使用过的交换区尚未被覆盖，该数值即为这些已存在于内存中的交换区的大小 |

对于内存监控，在 top 信息里我们要时刻监控第五行 swap 交换分区的 used，如果这个数值在不断的变化，说明内核在不断进行内存和 swap 的数据交换，此时说明系统内存容量不够使用了，需要进行内存扩容了

### 第二部分

第二部分为进程的详细信息展示

```
PID  USER PR NI VIRT   RES    SHR   S  %CPU %MEM  TIME+    COMMAND
2281 root 20   0 1050296  160608 40316  S  18.2  0.2  14:37.41 node
```

| 展示信息  |                                   含义                                    |
| :-------: | :-----------------------------------------------------------------------: |
|   `PID`   |                                  进程 id                                  |
|  `USER`   |                                进程所有者                                 |
|   `PR`    |                                进程优先级                                 |
|   `NI`    |                nice 值，负值表示高优先级，正值表示低优先级                |
|  `VIRT`   |              进程使用的虚拟内存总量，单位 kb，VIRT=SWAP+RES               |
|   `RES`   |        进程使用的、未被换出的物理内存大小，单位 kb，RES=CODE+DATA         |
|   `SHR`   |                           共享内存大小，单位 kb                           |
|    `S`    | 进程状态（D=不可中断的睡眠状态，R=运行，S=睡眠，T=跟踪/停止，Z=僵尸进程） |
|  `%CPU`   |                    上次更新到现在的 CPU 时间占用百分比                    |
|  `%MEM`   |                         进程使用的物理内存百分比                          |
|  `TIME+`  |                  进程使用的 CPU 时间总计，单位 1/100 秒                   |
| `COMMAND` |                         进程名称（命令名/命令行）                         |

默认进入 top 时，各进程是按照 CPU 的占用量来排序的，

其中`%CPU`指的是单位时间内进程使用的 CPU 时间百分比；一方面，该进程占用的 CPU，表示的是系统中总共 CPU 的占有率，可以大于 100%，如果是 8 核的系统，可以到达`800%`，另一方面，如果占用率到达 100%也并不代表完全占满了一个核心，只能说是在逻辑上等价完全占用了一个核心，实际上如果在双核心的系统上，每个核心各占 50%也说不定

## top 交互命令

### 详细展示每个 CPU 使用情况

进入 top 视图的时候，按`1`将详细展示出每颗 CPU 的占用情况，展示效果如下：

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20211025115840582.png)

### 自定义进程展示信息

通过 f 键可以选择显示的内容。按 f 键之后会显示列的列表，按 a-z 即可显示或隐藏对应的列，最后按回车键确定

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20211025120159880.png)

### 其他交互命令

```bash
h：显示帮助画面，给出一些简短的命令总结说明；
k：终止一个进程；
i：忽略闲置和僵死进程，这是一个开关式命令；
q：退出程序；
r：重新安排一个进程的优先级别；
S：切换到累计模式；
s：改变两次刷新之间的延迟时间（单位为s），如果有小数，就换算成ms。
输入0值则系统将不断刷新，默认值是5s；

l：切换显示平均负载和启动时间信息；
m：切换显示内存信息；
t：切换显示进程和CPU状态信息；
c：切换显示命令名称和完整命令行；
M：根据驻留内存大小进行排序；
P：根据CPU使用百分比大小进行排序；
T：根据时间/累计时间进行排序；
w：将当前设置写入~/.toprc文件中
```

## 常见问题汇总

下面列出在使用 `top` 命令时，出现的一些疑惑点，并给出相关的解答

### 出现 `kswapd0` 进程 CPU 占用过高

swap 分区的作用是**当物理内存不足时，会将一部分硬盘当做虚拟内存来使用**。kswapd0 占用过高是因为物理内存不足，使用 swap 分区与内存换页操作交换数据，导致 CPU 占用过高

{% alert success no-icon%}
可以通过修改 `/etc/sys/vm/swappiness` 里面的数值来修改 swap 分区使用与否，默认 60，数值越大表示更多的使用 swap 分区。这个交换参数控制内核从物理内存移出进程，移到交换空间。该参数从 0 到 100，当该参数=0，表示只要有可能就尽力避免交换进程移出物理内存;该参数=100，这告诉内核疯狂的将数据移出物理内存移到 swap 缓存中。设置 `vm.swappiness=0` 后并不代表禁用 swap 分区，只是告诉内核，能少用到 swap 分区就尽量少用到，设置 vm.swappiness=100 的话，则表示尽量使用 swap 分区
{%endalert%}
:question:Swap 的分配会对系统性能产生影响吗？
{% alert success no-icon%}
swap 分配策略不同，会对系统性能产生较明显的影响。分配太多的 Swap 空间会浪费磁盘空间，而 Swap 空间太少，则系统会发生错误。如果系统的物理内存用光了，系统会跑得很慢（大部分 CPU 占用率用于进行内存页的扇入和扇出），但仍能运行；如果 Swap 空间用光了，那么系统就会发生错误
{%endalert%}
下面举一个例子：
{% alert warning no-icon%}
例如，Web 服务器能根据不同的请求数量衍生出多个服务进程（或线程），如果 Swap 空间用完，则服务进程无法启动，通常会出现“application is out of memory”的错误，严重时会造成服务进程的死锁。因此 Swap 空间的分配是很重要的
{%endalert%}

:question:不同的 swap 分配策略会严重影响到系统性能，那么到底应该如何分配 swap 分区？
{% alert success no-icon%}
通常情况下，Swap 空间应大于或等于物理内存的大小，最小不应小于 64M，通常 Swap 空间的大小应是物理内存的 2-2.5 倍。

**但根据不同的应用，应有不同的配置**：如果是小的桌面系统，则只需要较小的 Swap 空间，而大的服务器系统则视情况不同需要不同大小的 Swap 空间。特别是数据库服务器和 Web 服务器，随着访问量的增加，对 Swap 空间的要求也会增加，一般来说对于 4G 以下的物理内存，配置 2 倍的 swap，4G 以上配置 1 倍
{%endalert%}

另一方面，Swap 分区的数量对性能也有很大的影响。因为 Swap 交换的操作是磁盘 IO 的操作，如果有多个 Swap 交换区，Swap 空间的分配会以轮流的方式操作于所有的 Swap，这样会大大均衡 IO 的负载，加快 Swap 交换的速度。如果只有一个交换区，所有的交换操作会使交换区变得很忙，使系统大多数时间处于等待状态，效率很低。用性能监视工具会发现，此时 CPU 负载并不高，而系统却慢。这说明，系统的瓶颈在 IO 上，依靠提高 CPU 的速度解决不了问题，需要增大物理内存或者减少交互分区的使用率

## 附录

[Linux - top 命令查看服务器 CPU 与内存占用](https://blog.csdn.net/J080624/article/details/80526310)
[Linux 中 top 命令输出指标详解](https://www.jianshu.com/p/af584c5a79f2)
[Linux kswapd0 进程 CPU 占用过高](https://www.cnblogs.com/aaron911/p/11002338.html)
