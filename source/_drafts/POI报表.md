---
title: POI报表解决方案
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: 解决方案
tags: 杂七杂八
keywords: POI报表
excerpt: 本文主要讲解如何通过 Java 进行报表处理
---
## 概述

在企业级应用开发中，Excel报表是一种最常见的报表需求。Excel报表开发一般分为两种形式：

- 方便操作，基于Excel的报表批量上传数据
- 通过java代码生成Excel报表

在SaaS-HRM系统中，也有大量的报表操作

## Excel的两种形式

目前世面上的Excel分为两个大的版本Excel2003和Excel2007及以上两个版本，两者之间的区别如下：

|               |                Excel 2003                |              Excel 2003               |
| :-----------: | :--------------------------------------: | :-----------------------------------: |
|     后缀      |                  `xls`                   |                `xlsx`                 |
|     结构      | 二进制格式，核心结构是复合文档类型的结构 |              XML类型结构              |
| 单sheet数据量 |          行：65535<br />列：256          |      行：1048576<br />列：16384       |
|     特点      |               存储容量优先               | 基于`xml`压缩，占用空间小、操作效率高 |

:sparkles:Java中常见的用来操作Excl的方式一般有2种：JXL和POI

- JXL只能对Excel进行操作,属于比较老的框架，它只支持到Excel 95-2000的版本。现在已经停止更新和维护。
- POI是apache的项目,可对微软的Word,Excel,Ppt进行操作,包括office2003和2007,Excl2003和2007。poi现在 一直有更新

## POI

APache POI是Apache软件基金会的开源项目，由 Java 编写的免费开源的跨平台的 Java API，Apache POI提供API给Java语言操作`Microsoft Office`功能

:sparkles:应用场景

- 数据报表生成
- 数据备份
- 数据批量上传

```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi</artifactId>
            <version>4.0.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi-ooxml</artifactId>
            <version>4.0.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.poi</groupId>
            <artifactId>poi-ooxml-schemas</artifactId>
            <version>4.0.1</version>
        </dependency>
    </dependencies>
```

结构说明

| 结构 |                   对应的office                   |
| :--: | :----------------------------------------------: |
| HSSF |    提供读写Microsoft Excel XLS格式档案的功能     |
| XSSF | 提供读写Microsoft Excel OOXML XLSX格式档案的功能 |
| HWPF |     提供读写Microsoft Word DOC格式档案的功能     |
| HSLF |    提供读写Microsoft PowerPoint格式档案的功能    |
| HDGF |       提供读Microsoft Visio格式档案的功能        |
| HPBF |     提供读Microsoft Publisher格式档案的功能      |
| HSMF |      提供读Microsoft Outlook格式档案的功能       |



### 操作

|  API名称  |                             含义                             |
| :-------: | :----------------------------------------------------------: |
| Workbook  | Excel的文档对象,针对不同的Excel类型分为：HSSFWorkbook（2003）和 XSSFWorkbool（2007） |
|   Sheet   |                         Excel的表单                          |
|    Row    |                          Excel的行                           |
|   Cell    |                       Excel的格子单元                        |
|   Font    |                          Excel字体                           |
| CellStyle |                         格子单元样式                         |

