---
title: 论文阅读记录（四）
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 计算机算法理论
tags: 联邦学习
keywords:
  - 联邦学习
  - Non-iid
excerpt: 2021-11月29日至2021-12月5日的论文阅读笔记
thumbnailImage:
---
## 进展

本周主要确定下来几件事情：

1. 

## 论文笔记

### LstFcFedLear: A LSTM-FC with Vertical Federated Learning Network for Fault Prediction



### Failure prediction in production line based on federated learning: an empirical study



#### research questions

private data belongs to multiple independent organizations and cannot be  shared with others.   

Although FL has achieved success in many fields, whether it can achieve similar performance and replace CL in real-world manufacturing is still an open question.  

Accordingly, this paper bridges this gap by reporting an empirical study of defective product prediction in the production line by comparing FL with CL. This study is the first attempt to design FL algorithms for failure prediction in the production line

construct a horizontal FL (HFL) scenario and a vertical FL (VFL) scenario, respectively.In the former, we compared the Federated Support Vector Machine (FedSVM) and the support vector machine  (SVM) algorithms. In the latter, we compare the federated random forest (FedRF) and the random forest (RF) algorithms.   

主要研究以下四个问题：



#### hypotheses

This article focuses on the **HFL and VFL scenarios**. 

In HFL, data are partitioned by user IDs or device IDs. For example, in the context of manufacturing, clients A and B are two independent factories having the same production line structure and machine configuration. The data features in each production line are roughly the same, while the product IDs on their production lines do not overlap.  

VFL is applicable to the cases that two datasets share the same sample ID space but differ in feature space. For example, production lines A and B are independent, but one belongs to the upstream, and  the other belongs to the downstream of the entire production line. Their product IDs are likely to be the same, which guarantees the intersection of their product space.   

:question:那么模型训练的目标是得到什么？

#### method/measures used


#### key findings

### A Vertical Federated Learning Method for Interpretable Scorecard and Its Application in Credit Scoring



### Predict Failures in Production Lines A Two-stage Approach with Clustering and Supervised Learning



## 额外补充的知识

| 相关算法 |                        机器学习库学习                        |                           基础知识                           |
| :------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|          |    [特征处理经验](https://zhuanlan.zhihu.com/p/166440574)    | [2.1. 数据操作](https://zh-v2.d2l.ai/chapter_preliminaries/ndarray.html) |
|          | [一日一学--如何对数值型特征进行分桶](https://cloud.tencent.com/developer/article/1590912) | [【机器学习】逻辑回归（非常详细）](https://zhuanlan.zhihu.com/p/74874291) |
|          | [特征工程之特征缩放&特征编码](https://zhuanlan.zhihu.com/p/56902262) | [Bosch Production Line Performance](https://www.kaggle.com/c/bosch-production-line-performance/data) |
|          | [线性回归模型与pytorch实现](https://zhuanlan.zhihu.com/p/86982616) |                                                              |

