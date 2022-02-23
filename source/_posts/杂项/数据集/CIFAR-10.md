---
title: CIFAR-10
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-02-23 10:05:56
thumbnailImage:
categories: 通用技能
tags: 杂七杂八
keywords: cifar
excerpt: 本文主要介绍 cifar-10 数据集
---
<!-- toc -->
## 概述

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/dl/dataset/dl-dataset-cifar-10.png)



CIFAR-10是一个更接近普适物体的彩色图像数据集。CIFAR-10 是由 Hinton 的学生Alex Krizhevsky 和Ilya Sutskever 整理的一个用于识别普适物体的小型数据集。一共包含10 个类别的RGB 彩色图片：飞机（ airplane ）、汽车（ automobile ）、鸟类（ bird ）、猫（ cat ）、鹿（ deer ）、狗（ dog ）、蛙类（ frog ）、马（ horse ）、船（ ship ）和卡车（ truck ）
{% alert success no-icon %}

:sparkles: 每个图片的尺寸为32 × 32 ，每个类别有 6000 个图像，数据集中一共有 50000 张训练图片和 10000 张测试图片

{% endalert %}

:books:官网链接：[The CIFAR-10 dataset](http://www.cs.toronto.edu/~kriz/cifar.html)

:vs: 与 MNIST 数据集中目比， CIFAR-10 具有以下不同点:
{% alert info no-icon %}

- CIFAR-10 是 3 通道的彩色RGB 图像，而 MNIST 是**灰度图像**
- CIFAR-10 的图片尺寸为 32 × 32 ， 而 MNIST 的图片尺寸为28 × 28 
- 相比于手写字符， CIFAR-10 含有的是现实世界中真实的物体，不仅噪声很大，而且物体的比例、特征都不尽相同，这为识别带来很大困难。直接的线性模型如Softmax 在CIFAR-10 上表现得很差

{% endalert %}

## 数据文件名

下载并解压数据集后，是如下形式：

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/dl/dataset/dl-dataset-cifar-datafile.png)

|      文件名       |                           文件用途                           |
| :---------------: | :----------------------------------------------------------: |
|   batches.meta    |                文件存储了每个类别的英文名称。                |
| data_batch_{1..5} | 这5 个文件是CIFAR- 10 数据集中的训练数据。每个文件以二进制格式存储了10000 张32 × 32 的彩色图像和这些图像对应的类别标签。一共50000 张训练图像 |
|    test_batch     |    这个文件存储的是测试图像和测试图像的标签。一共10000 张    |

## 附录

[数据集——CIFAR-10](https://blog.csdn.net/qq_41185868/article/details/82793025)

[使用python转换CIFAR-10]:https://blog.csdn.net/weixin_42650424/article/details/103436489?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link
