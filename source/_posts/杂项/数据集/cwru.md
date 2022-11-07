---
title: CWRU 数据集
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-02-23 10:05:56
thumbnailImage:
categories: 通用技能
tags: 杂七杂八
keywords: CWRU
excerpt: 本文主要介绍故障诊断中常用的数据集——CWRU
---

<!-- toc -->

## 概述

CWRU 数据集是由凯斯西储大学提供的滚珠轴承测试数据。实验使用 2 马力的电机进行，并在电机轴承附近和远处测量了加速度数据。通过电火花加工技术引入人工电机轴承故障。从直径 0.007 英寸到直径 0.040 英寸的故障分别出现在内滚道、滚动元件（即球）和外滚道。将故障轴承重新安装到测试电机中，记录 0 ～ 3 马力（电机转速从 1797 到 1729）对电机负载的振动数据。

## 实验平台

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/dl/dataset/dataset-cwru.png)

用于获取 CWRU 数据集的轴承实验台装置如图所示，包括一个 2 马力电动机、一个扭矩传感器/编码器、一个测力计和控制电子器件组成（在图中没有显示），实验轴承带动着电机杆进行旋转。
扭矩通过测力计和电子控制系统施加到轴上。分别在滚动体（Ball）、内圈（Inner Race）和外圈（Outer Race）上施加缺陷模拟轴承故障，然后将每个故障轴承安装在试验台上，启动电机，加速度传感器开始测量并收集数据。

扭矩通过测力计和电子控制系统施加到轴上。分别在滚动体（Ball）、内圈（Inner Race）和外圈（Outer Race）上施加缺陷模拟轴承故障，然后将每个故障轴承安装在试验台上，启动电机，加速度传感器开始测量并收集数据。

## 数据测量过程介绍

振动数据是通过安装在带有磁性底座的外壳上的加速度计来收集的。加速度计被放置在电动机的驱动端和风扇端的 12 点钟位置。加速度数据是在靠近和远离电机轴承的位置测量振动的；数据是由放置在三个位置的传感器收集的，三个位置分别为电机轴承的驱动端（Drive-end）、风扇端（Fan-end）的 12 点钟位置和电机支撑基座（Normal-baseline）。最终，所有数据文件以 MATLAB(\*.mat)格式存储。每个文件包含一个或多个记录的 DE（Drive-end）、FE（Fan-end）和 BA（Normal-baseline）加速度数据。

对于不同轴承实验，实验采集频率如下：

{% alert success no-icon %}

- 对于 DE 轴承实验，数据采集速度为每秒 12k 和 48k 个样本
- 对于 FE 轴承实验，采集速度为每秒 12k 个样本；
- 对于 BA 轴承实验，数据收集速率为每秒 48k

{% endalert %}

数字数据采集速度为每秒 12000 个样本，驱动端轴承故障数据采集速度为每秒 48000 个样本。使用扭矩传感器/编码器收集速度和马力数据，并手工记录

## 实验轴承故障大小参数介绍

使用电火花加工将单点故障引入测试轴承，故障直径为 7mils，14mils，21mils，28 mils 以及 40 mil。其中 SKF 轴承用于 7，14，21mils 故障直径的诊断，NTN 等效轴承用于 28 mil 和 40 mil 故障

外滚道故障是静止故障，因此故障相对于轴承负载区的位置对电机/轴承系统的振动响应有直接影响。为了量化这一影响，对位于 3 点（直接在负载区 ）、6 点（正交于负载区）和 12 点的具有外滚道故障的风扇和驱动端轴承进行了实验
该实验使用了两个类型轴承（SKF 和 NTN），其中 SKF 轴承用于 7、14、21mil 直径故障，对于 28mil 故障，使用 NTN 轴承。

| 轴承型号 | 故障直径大小（mil） |                              故障深度（mil）                              |
| :------: | :-----------------: | :-----------------------------------------------------------------------: |
|   SKF    |          7          |                               All fault: 11                               |
|   SKF    |         14          |                               All fault: 11                               |
|   SKF    |         21          |                               All fault: 11                               |
|   NTN    |         28          | Inner Raceway fault: 50<br />Outer Raceway fault: 50<br />Ball fault: 150 |

数据是以 Matlab 形式保存的，每一个文件包括了风扇端和驱动端的振动信号，同时还包括电机转速的信息。对于所有文件，下列变量的含义如下：

| 变量 |        含义        |
| :--: | :----------------: |
|  DE  | 驱动端加速度计数据 |
|  FE  | 风扇端加速度计数据 |
|  BA  |  基础加速度计数据  |
| time |    时间序列数据    |
| RPM  |        转速        |

其中 DE，FE，BA 分别是布置的三个传感器分别测的数据，说明在离故障源不同距离所测得的信号数据是否有效，实测是都能用的。

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/dl/dataset/dataset-cwru-faultDescription.png)

## 故障类别

|  Bearing  | Fault Location | Diameter | Depth | Bearing Manufacturer |
| :-------: | :------------: | :------: | :---: | :------------------: |
| Drive End | Inner Raceway  |   .007   | .011  |         SKF          |
| Drive End | Inner Raceway  |   .014   | .011  |         SKF          |
| Drive End | Inner Raceway  |   .021   | .011  |         SKF          |
| Drive End | Inner Raceway  |   .028   | .050  |         NTN          |
| Drive End | Outer Raceway  |   .007   | .011  |         SKF          |
| Drive End | Outer Raceway  |   .014   | .011  |         SKF          |
| Drive End | Outer Raceway  |   .021   | .011  |         SKF          |
| Drive End | Outer Raceway  |   .040   | .050  |         NTN          |
| Drive End |      Ball      |   .007   | .011  |         SKF          |
| Drive End |      Ball      |   .014   | .011  |         SKF          |
| Drive End |      Ball      |   .021   | .011  |         SKF          |
| Drive End |      Ball      |   .028   | .150  |         NTN          |
|  Fan End  | Inner Raceway  |   .007   | .011  |         SKF          |
|  Fan End  | Inner Raceway  |   .014   | .011  |         SKF          |
|  Fan End  | Inner Raceway  |   .021   | .011  |         SKF          |
|  Fan End  | Outer Raceway  |   .007   | .011  |         SKF          |
|  Fan End  | Outer Raceway  |   .014   | .011  |         SKF          |
|  Fan End  | Outer Raceway  |   .021   | .011  |         SKF          |
|  Fan End  |      Ball      |   .007   | .011  |         SKF          |
|  Fan End  |      Ball      |   .014   | .011  |         SKF          |
|  Fan End  |      Ball      |   .021   | .011  |         SKF          |

## 附录

[故障诊断学习｜ CWRU 数据集介绍 第 1 期](https://www.modb.pro/db/127848)
