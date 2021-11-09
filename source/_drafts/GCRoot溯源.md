---
title: GC工具使用
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: JVM
tags: JVM
keywords: GC工具使用
excerpt: 使用工具溯源
---
## MAT与JProfiler的GC Roots溯源

MAT是什么？

MAT是Memory Analyzer的简称，它是一款功能强大的Java堆内存分析器。用于查找内存泄漏以及查看内存消耗情况

MAT是基于Eclipse开发的，是一款免费的性能分析工具

可以在 http://www.eclipse.org/mat/ 下载并使用MAT

获取dump文件

##### 方式一：命令行使用 jmap



##### 方式二：使用JVisualVM导出

捕获的heap dump文件是一个临时文件，关闭JVisualVM后自动删除，若要保留，需要将其另存为文件

可通过以下方法捕获heap dump：

- 在左侧“Application"（应用程序）子窗口中右击相应的应用程序，选择Heap Dump（堆Dump）。 

-  在Monitor（监视）子标签页中点击Heap Dump（堆Dump）按钮

##### 方式三：使用MAT打开Dump文件