---
title: MNIST 数据集
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-02-23 10:17:56
thumbnailImage:
categories: 通用技能
tags: 杂七杂八
keywords: MNIST
excerpt: 本文主要介绍最经典的一个数据集—— MNIST
---

<!-- toc -->

## 概述

:question: MNIST 数据集是什么
{% alert success no-icon %}

MNIST 数据集是美国国家标准与技术研究院收集整理的大型手写数字数据库,包含 60,000 个示例的训练集以及 10,000 个示例的测试集

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/dl/dataset/dl-dataset-mnist.png %}

:books: 这是一个公开数据集，可以通过访问[官网](http://yann.lecun.com/exdb/mnist/)进行下载

## 数据文件名

官网中存在四个链接文件，各个文件具体含义如下：

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/dl/dataset/dl-dataset-mnist-datafile.png)

|           文件名           |          含义           |
| :------------------------: | :---------------------: |
| train-images-idx3-ubyte.gz | **Training set images** |
| train-labels-idx1-ubyte.gz | **Training set labels** |
| t10k-images-idx3-ubyte.gz  |   **Test set images**   |
| t10k-labels-idx1-ubyte.gz  |   **Test set labels**   |

## 数据集格式转换

考虑到数据集的大小，图片最终都是压缩存储的。因此，如果想要获取真正的图片数据，还需要一些读取操作。具体的操作步骤如下：

### 读取数据集

下面的函数就能够将官网下载下来的数据进行读取：

```python
def load_mnist(path, kind="train"):
    # Load MNIST data from `path`
    images_path = os.path.join(path, "%s-images.idx3-ubyte" % kind)
    labels_path = os.path.join(path, "%s-labels.idx1-ubyte" % kind)
    with open(labels_path, 'rb') as lb:
        magic, n = struct.unpack('>II', lb.read(8))
        labels = np.fromfile(lb, dtype=np.uint8)
    with open(images_path, 'rb') as imgpath:
        magic, num, rows, cols = struct.unpack('>IIII', imgpath.read(16))
        images = np.fromfile(imgpath, dtype=np.uint8).reshape(len(labels), 784)
    return images, labels
```

### 保存数据集为 csv 文件

下面的代码主要进行持久化工作，能够将读取到文件持久化保存到本地

```python
def saveFile(images, labels, test_images, test_label):
    np.savetxt("../train_img.csv", images, fmt="%i", delimiter=',')
    np.savetxt("../train_labels.csv", labels, fmt="%i", delimiter=',')
    np.savetxt("../test_img.csv", test_images, fmt="%i", delimiter=',')
    np.savetxt("../test_labels.csv", test_label, fmt="%i", delimiter=',')
```

### 从 csv 文件中导入

一旦将图片数据持久化到本地之后，后续就不再需要进行解压重新读取数据的操作了，只需要读取当时持久化保存的文件即可，具体的代码如下：

```python
X_train = np.genfromtxt('train_img.csv',
                        dtype=int, delimiter=',')
y_train = np.genfromtxt('train_labels.csv',
                        dtype=int, delimiter=',')
X_test = np.genfromtxt('test_img.csv',
                       dtype=int, delimiter=',')
y_test = np.genfromtxt('test_labels.csv',
                       dtype=int, delimiter=',')
```

:older_man: 从 CSV 文件中加载 MNIST 数据将会显著花费更长的时间, 因此如果可能的话, 还是建议维持数据集原有的字节格式

## 总结

:sparkles: 如今，即使是简单的模型也能达到 95% 以上的分类准确率，因此不适合区分强模型和弱模型，所以 MNIST 更像是⼀个健全检查，而不是⼀个基准。

## 附录

[MNIST 数据集](https://zhuanlan.zhihu.com/p/36592188)
[详解 MNIST 数据集](https://blog.csdn.net/simple_the_best/article/details/75267863)
