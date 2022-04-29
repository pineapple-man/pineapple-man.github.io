---
title: MySQL length()char_length()之间的区别
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 数据库
tags: MySQL
keywords: 数据库
excerpt: 前几天进行阿里笔试的时候遇到一个题，刚好没接触过，在这里记录一下
date: 2022-04-01 15:51:21
thumbnailImage:
---
<!-- toc -->

## length(str) 函数

此函数统计的是字节数，数字/字母在gbk或utf8编码下，一个数字/字母都占用1个字节，在utf8的编码下，中文字符占用3个字节。在gbk的编码下，中文字符占用2个字节。

## char_length(str)函数

此函数统计的是字符数，例如一个数字/字母/汉字，算为1个字符