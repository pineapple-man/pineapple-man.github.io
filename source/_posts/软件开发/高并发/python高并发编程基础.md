---
title: Python高并发编程基础
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories:
  - 高并发
tags:
  - Python
  - 高并发
keywords:
  - Python
  - 高并发
  - 多进程
  - 多线程
excerpt: 这是一篇关于Python高并发编程的文章
action: true
date: 2021-11-10 12:54:28
thumbnailImage:
---

<!-- toc -->

## 概述

解释器将 Python 转换成一种中间语言，叫做 Python 字节码，类似于汇编语言，但是包含一些更高级的指令。当运行一个 Python 程序的时候，评估循环不断将 Python 字节码转换成机器码。解释型语言的好处是**方便编程和调试，但是程序的运行速度慢**。Python 提供了很多可以利用并行的模块，我们将着重讨论这些并行编程的模块

## 基于线程的并行

Python 通过标准库的 `threading` 模块来管理线程，线程模块的主要组件如下：

- 线程对象
- Lock 对象
- RLock 对象
- 信号对象
- 条件对象
- 事件对象

使用线程最简单的一个方法是，用一个目标函数实例化一个 Thread 然后调用 `start()` 方法启动它。Python 的 threading 模块提供了 `Thread()` 方法在不同的线程中运行函数或处理过程等

```python
class threading.Thread(group=None,target=None,name=None,args=(),kwargs={})
```

- `group`: 一般设置为 `None` ，这是为以后的一些特性预留的
- `target`: 当线程启动的时候要执行的函数
- `name`: 线程的名字，默认会分配一个唯一名字 `Thread-N`
- `args`: 传递给 `target` 的参数，要使用 tuple 类型
- `kwargs`: 同上，使用字典类型 dict

:sailboat:当然也可以通过继承的方式实现多线程，使用 threading 模块实现一个新的线程，需要下面 3 步：

1. 定义一个 `Thread` 类的子类
2. 重写 `__init__(self [,args])` 方法，可以添加额外的参数
3. 重写 `run(self, [,args])` 方法来实现线程要做的事情

```python
import threading
import time

exitFlag = 0

class myThread (threading.Thread):
    def __init__(self, threadID, name, counter):
        threading.Thread.__init__(self)
        self.threadID = threadID
        self.name = name
        self.counter = counter

    def run(self):
        print("Starting " + self.name)
        print_time(self.name, self.counter, 5)
        print("Exiting " + self.name)
```

## 基于进程的并行

产生（spawn）的意思是：**由父进程创建子进程**，父进程既可以在产生子进程之后继续异步执行，也可以暂停等待子进程创建完成之后再继续执行。`Python`的`multiprocessing`库通过以下几步创建进程：

1. 创建进程对象
2. 调用 `start()` 方法，开启进程的活动
3. 调用 `join()` 方法，在进程结束之前一直等待

```python
import multiprocessing

def foo(i):
    print ('called function in process: %s' %i)
    return

if __name__ == '__main__':
    Process_jobs = []
    for i in range(5):
        p = multiprocessing.Process(target=foo, args=(i,))
        Process_jobs.append(p)
        p.start()
        p.join()
```

## 附录

[Python 并行编程](https://python-parallel-programmning-cookbook.readthedocs.io/zh_CN/latest/chapter1/08_Python_in_a_parallel_world.html)
