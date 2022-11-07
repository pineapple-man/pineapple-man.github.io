---
title: POI报表解决方案
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 解决方案
tags: 杂七杂八
keywords: POI报表
excerpt: 本文主要讲解如何通过 Java 进行报表处理
date: 2022-02-01 00:55:15
thumbnailImage:
---

<!-- toc -->

## 概述

在企业级应用开发中，Excel 报表是一种最常见的报表需求，Excel 报表开发一般分为两种形式：

- 方便操作，基于 Excel 的报表批量上传数据
- 通过 java 代码生成 Excel 报表

## Excel 的两种形式

目前世面上的 Excel 分为两个大的版本 Excel2003 和 Excel2007 及以上两个版本，两者之间的区别如下：

|                 |                Excel 2003                |              Excel 2003               |
| :-------------: | :--------------------------------------: | :-----------------------------------: |
|      后缀       |                  `xls`                   |                `xlsx`                 |
|      结构       | 二进制格式，核心结构是复合文档类型的结构 |             XML 类型结构              |
| 单 sheet 数据量 |          行：65535<br />列：256          |      行：1048576<br />列：16384       |
|      特点       |               存储容量优先               | 基于`xml`压缩，占用空间小、操作效率高 |

:sparkles:Java 中常见的用来操作 Excl 的方式一般有 2 种：JXL 和 POI

- JXL 只能对 Excel 进行操作,属于比较老的框架，它只支持到 Excel 95-2000 的版本。现在已经停止更新和维护。
- POI 是 apache 的项目,可对微软的 Word、Excel、PPT 进行操作,包括 office2003 和 2007,Excel2003 和 2007

## POI

APache POI 是 Apache 软件基金会的开源项目，由 Java 编写的免费开源的跨平台的 Java API，Apache POI 提供 API 给 Java 语言操作`Microsoft Office`功能

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

|  结构  |                     对应的 office                      |
| :----: | :----------------------------------------------------: |
| `HSSF` |    提供读写 **Microsoft Excel XLS** 格式档案的功能     |
| `XSSF` | 提供读写 **Microsoft Excel OOXML XLSX** 格式档案的功能 |
|  HWPF  |       提供读写 Microsoft Word DOC 格式档案的功能       |
|  HSLF  |      提供读写 Microsoft PowerPoint 格式档案的功能      |
|  HDGF  |         提供读 Microsoft Visio 格式档案的功能          |
|  HPBF  |       提供读 Microsoft Publisher 格式档案的功能        |
|  HSMF  |        提供读 Microsoft Outlook 格式档案的功能         |

### 操作

| API 名称  |                                          含义                                           |
| :-------: | :-------------------------------------------------------------------------------------: |
| Workbook  | Excel 的文档对象,针对不同的 Excel 类型分为：HSSFWorkbook（2003）和 XSSFWorkbool（2007） |
|   Sheet   |                                      Excel 的表单                                       |
|    Row    |                                       Excel 的行                                        |
|   Cell    |                                    Excel 的格子单元                                     |
|   Font    |                                       Excel 字体                                        |
| CellStyle |                                      格子单元样式                                       |

### 创建 `Excel`

```java
XSSFWorkbook workbook = new XSSFWorkbook();
XSSFSheet sheet = workbook.createSheet("test");
FileOutputStream fileOutputStream = new FileOutputStream("test.xlsx");
workbook.write(fileOutputStream);
fileOutputStream.close();
```

### 创建单元格

```java
XSSFWorkbook workbook = new XSSFWorkbook();
XSSFSheet sheet = workbook.createSheet("test");
XSSFRow row = sheet.createRow(3);
XSSFCell cell = row.createCell(0);
cell.setCellValue("Hello World");
FileOutputStream fileOutputStream = new FileOutputStream("test.xlsx");
workbook.write(fileOutputStream);
fileOutputStream.close();
```

### 设置格式

```java
// 创建单元格样式对象
XSSFCellStyle cellStyle = workbook.createCellStyle();

// 设置边框
// 设置下边框
cellStyle.setBorderBottom(BorderStyle.DASH_DOT);
// 设置上边框
cellStyle.setBorderBottom(BorderStyle.HAIR);

// 设置字体
// 创建字体对象
Font font = workbook.createFont();
// 设置字体
font.setFontName("仿宋");
// 设置字号
font.setFontHeightInPoints((short) 28);
cellStyle.setFont(font);

// 设置宽高
// 设置第一列的宽度是 31 个字符宽度
sheet.setColumnWidth(0,31*256);
// 设置行的高度是 50 个点
row.setHeightInPoints(50);

// 设置居中显示
// 水平居中
cellStyle.setAlignment(HorizontalAlignment.CENTER);
// 垂直居中
cellStyle.setVerticalAlignment(VerticalAlignment.CENTER);

// 设置单元格样式
cell.setCellStyle(cellStyle);

// 合并单元格
CellRangeAddress region = new CellRangeAddress(0, 3, 0, 3);
sheet.addMergedRegion(region);
fileOutputStream.close();
```

### 绘制图形

```java
		XSSFWorkbook workbook = new XSSFWorkbook();
		XSSFSheet sheet = workbook.createSheet("test");
		// 读取图片
		FileInputStream fileInputStream = new FileInputStream("/Users/humingchao/IdeaProjects/SaaS_ihrm/poiTest/p1.jpg");
		byte[] bytes = IOUtils.toByteArray(fileInputStream);
		fileInputStream.read(bytes);

		// 向 POI 内存添加一张图片，并返回该图片在 Excel 中的图片集合中的下标
		int index = workbook.addPicture(bytes, Workbook.PICTURE_TYPE_JPEG);
		// 绘制图片工具类
		XSSFCreationHelper helper = workbook.getCreationHelper();
		// 创建一个绘图对象
		Drawing<?> patriarch = sheet.createDrawingPatriarch();
		// 创建锚点，设置图片坐标
		ClientAnchor anchor = helper.createClientAnchor();
		// 行从 0 开始
		anchor.setRow1(0);
		// 列从 0 开始
		anchor.setCol1(0);

		// 创建图片
		Picture picture = patriarch.createPicture(anchor, index);
		picture.resize();

		// 文件流
		FileOutputStream fileOutputStream = new FileOutputStream("test3.xlsx");
		// 写入文件
		workbook.write(fileOutputStream);
		fileOutputStream.close();
```

### 加载 Excel

```java
public static void main(String[] args) throws Exception {
   // 创建 workbook 工作簿
   XSSFWorkbook workbook = new XSSFWorkbook("demo.xlsx");
   // 获取 sheet 从 0 开始
   Sheet sheet = workbook.getSheetAt(0);

   int totalRowNum = sheet.getLastRowNum();

   Row row = null;
   Cell cell = null;

  // 循环所有行
   for (int rowNum = 0; rowNum <=sheet.getLastRowNum(); rowNum++) {
      row = sheet.getRow(rowNum);
      StringBuilder stringBuilder = new StringBuilder();
     // 循环每行中的所有单元格
      for (int cellNum = 2; cellNum < row.getLastCellNum(); cellNum++) {
         cell = row.getCell(cellNum);
         stringBuilder.append(getValue(cell)).append("-");
      }
      System.out.println(stringBuilder.toString());
   }
}

// 获取数据
public static Object getValue(Cell cell) {
   Object value = null;
   switch (cell.getCellType()) {
      case STRING:
         value = cell.getStringCellValue();
         break;
      case BOOLEAN:
         value = cell.getBooleanCellValue();
         break;
		// 数字类型（包含日期和普通数字）
     case NUMERIC:
         if (DateUtil.isCellDateFormatted(cell)) {
            value = cell.getDateCellValue();
         } else {
            value = cell.getNumericCellValue();
         }
         break;
		// 公式类型
     case FORMULA:
         value = cell.getCellFormula();
         break;
      default:
         break;
   }
   return value;
}
```

## 模版打印

自定义生成 Excel 报表文件还是有很多不尽如意的地方，特别是针对复杂报表头，单元格样式，字体等操作。手写这些代码不仅费时费力，有时候效果还不太理想。

:question: 那怎么样才能更方便的对报表样式，报表头进行处理呢?

答案是使用已经准备好的 Excel 模板，只需要关注模板中的数据即可

:sailboat: 模版打印的操作步骤

1. 制作模版文件（模版文件的路径）
2. 导入（加载）模版文件，从而得到一个工作簿
3. 读取工作表
4. 读取行
5. 读取单元格
6. 读取单元格样式
7. 设置单元格内容
8. 其他单元格就可以使用读到的样式了
