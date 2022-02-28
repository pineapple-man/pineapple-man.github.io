---
title: KAT 数据集
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-02-23 10:30:56
thumbnailImage:
categories: 通用技能
tags: 杂七杂八
keywords: kat
excerpt: 本文主要介绍故障诊断中使用的一个数据集：KAT
---
<!-- toc -->
## 概述

德国帕德博恩大学 Chair of Design and Drive Technology 基于振动和电机电流信号的状态监测（CM）收集滚珠轴承数据集。通过定子电流信号的特征，识别有缺陷的轴承，目的是再现相关故障和损坏，以便通过试验获得所需的数据集。此数据集是一个 6203 轴承数据集，包括人为诱发的故障和真实的故障。数据收集实验平台如下图，各个部分的含义如下：

| 实验部分 |       介绍       |
| :------: | :--------------: |
|  （1）   |      电动机      |
|  （2）   |    扭矩测量轴    |
|  （3）   | 滚动轴承测试模块 |
|  （4）   |       飞轮       |
|  （5）   |     负载电机     |

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/dl/dataset/dl-dataset-KAt.png)

相比于使用电机振动数据进行故障分类，使用电机电流信号进行故障诊断的准确率较低，轴承损伤的检测通常是通过使用加速度传感器进行振动分析来监测的。该诊断方法利用机电驱动系统的电机电流信号以及振动信号进行轴承故障诊断，

该数据集采集并保存了试验台内运行的轴承振动信号，采样率为64khz。轴承以1500转/分的转速运行，负载转矩为0.1 Nm，轴承上的径向力为1000 N。轴承有三种可能的状态:健康、内圈故障和外圈故障。这些故障要么是人为方法造成的，要么是自然运行造成的

## 故障类别说明

人为和实际的损伤有内环和外环两种类型。每个轴承文件进行20次测量，每个文件在64 KHz采样率下采集，持续4秒。因此，数据文件大约有256000个数据点。实验在4种不同的工况下进行。不同工况的参数，如:转速、负载转矩、径向力。转速在900 rpm到1500 rpm之间变化，负载转矩从0.1 Nm变化到0.7 Nm，所述径向力从400N增加到1000N。下表给出了4种不同操作条件的设置。

| No.  | Rotational Speed [rpm] | Load Torque [Nm] | Radial force [N] | Name of setting |
| :--: | :--------------------: | :--------------: | :--------------: | :-------------: |
|  0   |          1500          |       0.7        |       1000       |   N15_M07_F10   |
|  1   |          900           |       0.7        |       1000       |   N09_M07_F10   |
|  2   |          1500          |       0.1        |       1000       |   N15_M01_F10   |
|  3   |          1500          |       0.7        |       400        |   N15_M07_F04   |

## 数据集损伤类别

以高分辨率和采样率同步测量 26 中轴承损坏状态和 6 种未损坏（健康）状态的电机电流和振动信号以供参考。该数据由32个不同的轴承实验数据组成。其中12个轴承是人为损坏的轴承，14个轴承在加速寿命试验中损坏，剩下的6个是健康轴承。

### 健康轴承数据集

| No.  | Rotational Speed [rpm] | Load Torque [Nm] | Radial force [N] | Name of setting |
| :--: | :--------------------: | :--------------: | :--------------: | :-------------: |
|  0   |          1500          |       0.7        |       1000       |   N15_M07_F10   |
|  1   |          900           |       0.7        |       1000       |   N09_M07_F10   |
|  2   |          1500          |       0.1        |       1000       |   N15_M01_F10   |
|  3   |          1500          |       0.7        |       400        |   N15_M07_F04   |

### 人为损坏轴承数据集

the artificial damage used in this dataset were caused by three different methods:

1. electric discharge machining (trench of 0.25 mm length in rolling direction and depth of 1-2 mm),
2. drilling (diameter: 0.9 mm, 2 mm, 3 mm), and 
3. manual electric engraving (damage length from 1-4 mm).

| Bearing Code | Component | Extent of Damage (Level) | Damage Method |
| :----------: | :-------: | :----------------------: | :-----------: |
|KA01 |OR| 1| EDM|
|KA03 |OR| 2| electric engraver|
|KA05 |OR| 1| electric engraver|
|KA06 |OR| 2| electric engraver|
|KA07 |OR| 1| drilling|
|KA08 |OR| 2| drilling|
|KA09 |OR| 2| drilling|
|KI01 |IR| 1| EDM|
|KI03 |IR| 1| electric engraver|
|KI05 |IR| 1| electric engraver|
|KI07 |IR| 2| electric engraver|
|KI08 |IR| 2| electric engraver|

| 缩写 |    含义    |
| :--: | :--------: |
|  OR  | outer ring |
|  IR  | inner ring |

### 真实损坏轴承数据集
|Bearing Code|Damage(main mode and symptom)|Bearing element|Combination|Arrangement|Extent of damage|Characteristic of damage|
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|KA04 |fatigue: pitting| OR| S| no repetition| 1| single point|
|KA15 |Plastic deform.: Indentations |OR| S| no repetition| 1| single point|
|KA16 |fatigue: pitting| OR| R| random| 2| single point|
|KA22 |fatigue: pitting| OR| S| no repetition| 1| single point
|KA30 |Plastic deform.: Indentations| OR| R| random| 1| distributed|
|KB23 |fatigue: pitting| IR(+OR)| M| random| 2| single point|
|KB24 |fatigue: pitting| IR(+OR)| M| no repetition| 3| distributed|
|KB27 |Plastic deform.: Indentations| OR +IR |M|random| 1|distributed|
|KI04 |fatigue: pitting| IR| M |no repetition| 1| single point|
|KI14 |fatigue: pitting| IR| M |no repetition| 1| single point|
|KI16 |fatigue: pitting| IR| S| no repetition| 3 |single point|
|KI17 |fatigue: pitting| IR| R| random| 1| single point|
|KI18 |fatigue: pitting| IR| S| no repetition| 2| single point|
|KI21 |fatigue: pitting| IR| S| no repetition| 1| single point|

| 缩写 |       含义        |
| :--: | :---------------: |
|  OR  |    outer ring     |
|  IR  |    inner ring     |
|  S   |   Single damage   |
|  R   | Repetitive damage |
|  M   |  multiple damage  |

:sparkles: 该数据集具有如下特点：

- 同步测量 26 种损坏轴承状态和 6 种健康状态的高分辨率和采样率的电机电流和振动信号
- 支持速度、扭矩、径向负载和温度的测量
- 四种不同的工况
- 每种工况采集共采集 20 次数据，每次采集时间持续 4 秒，最终保存为一个 matlab 文件，包含运行条件代码和四位轴承代码（如N15_M07_F10_KA01_1.mat)的名称

## 附录

[德国 帕德博恩大学 Bearing DataCenter](https://github.com/hustcxl/Rotating-machine-fault-data-set/blob/master/doc/Paderborn.md)
[2020-11-03Paderborn大学轴承数据集](https://blog.csdn.net/qq_42237342/article/details/109462267)
[帕德伯恩大学轴承数据集（PU）介绍](https://blog.csdn.net/m0_47180208/article/details/118268628)
[Data Description and Preprocessing](https://github.com/mohamedr002/AMDA/blob/master/KAt%20Pre-processing/Data%20Description%20and%20Preprocessing.ipynb)
[PADERBORN_Datase](https://github.com/mdzalfirdausi/CNN-for-Paderborn-Bearing-Dataset/blob/main/PADERBORN_Dataset.ipynb)
