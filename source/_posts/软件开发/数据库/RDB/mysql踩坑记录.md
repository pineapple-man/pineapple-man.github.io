---
title: mysql踩坑记录
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-01-30 13:25:56
thumbnailImage:
categories: 数据库
tags: Database
keywords: mysql
excerpt: 本文主要记录平时使用 MySQL 时遇到的问题
---
<!-- toc -->
本文主要记录平时使用 MySQL 时遇到的问题
## 字符集问题

```mysql
SELECT CONCAT(IFNULL(commission_pct,'无'),',',job_id) AS 'OUT_PUT' FROM employees;
Illegal mix of collations (utf8_general_ci,COERCIBLE), (utf8_general_ci,COERCIBLE), 
(gb2312_chinese_ci,IMPLICIT) for operation 'concat'
```





## mysql插入中文，报错：ERROR 1366 (HY000): Incorrect string value: '\xE5\xBC\xA0\xE4\xB8\x89' for column 'name

https://blog.csdn.net/u013155359/article/details/94392050



## 彻底解决mysql中文乱码

https://blog.csdn.net/u012410733/article/details/61619656

## 解决：The server time zone value '�й���׼ʱ��' is unrecognized or represents more than one time zone

https://blog.csdn.net/wxb141001yxx/article/details/104959538


## 附录
[远程mysql出现ERROR 1130 (HY000): Host '172.17.42.1' is not allowed to connect to this MySQL server](https://www.cnblogs.com/cuizhipeng/p/4386856.html)
[docker下的Mysql镜像的使用方法](https://blog.csdn.net/StemQ/article/details/52934795)
[MYSQL表名大小写设置](https://blog.csdn.net/Quincylk/article/details/103022485)
[Docker容器内Mysql大小写敏感方案解决](https://blog.csdn.net/an1090239782/article/details/103087058)
[连接器的区别](https://blog.csdn.net/xueyepiaoling/article/details/6160700)