---
title: MyBatis 逆向工程
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2021-10-24 00:16:06
thumbnailImage:
categories: 中间件
tags: 
    - MyBatis
keywords:
    - MyBatis 逆向工程
excerpt: 本文是MyBatis逆向工程的学习笔记
---
<!-- toc -->
## 概述

:question:什么是逆向工程？

- 根据数据表table，逆向分析数据表，自动生成`JavaBean`,`Dao Interface(mapper)`和`dao.xml`的一种技术
- MyBatis Generator（简称MBG），是一个专门为`MyBatis`框架使用者定制的代码生成器，能够快速的根据数据库逆向生成需要书写的Java代码

如果没有逆向工程，正常的开发流程应该采取如下步骤：

![正常开发流程](https://gitee.com/mingchaohu/blog-image/raw/master/image/20211024002032.png)

:notes:但是如果使用了MyBatis逆向工程后，仅需要简单的配置，就可以自动生成繁琐且没有技术含量的`Dao、Mapper`等文件

:sparkles:MBG特点

- 可以快速的根据数据库表生成对应的映射文件，接口以及bean类
- 支持基本的增删改查以及QBC风格的条件查询
- 表连接、存储过程等复杂SQL的定义需要手工编写

## MBG 使用

MyBatis 的逆向工程使用起来非常方便，仅需要两步（**配置依赖**和**书写逆向工程文件**）就可以加速项目的推进，自动生成相关的类文件和配置文件

### 导包

增加`MyBatis-gen`插件，并且需要增加`mybatis-generator-core`相关依赖

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.generator/mybatis-generator-core -->
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.3.5</version>
</dependency>
```

```xml
<!--mybatis 代码自动生成插件-->
<plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
    <version>1.3.6</version>
    <configuration>
        <!--配置文件的位置-->
        <configurationFile>GeneratorMapper.xml</configurationFile>
        <verbose>true</verbose>
        <overwrite>true</overwrite>
    </configuration>
</plugin>
```

### 编写 `mbg.xml` 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <classPathEntry location="/Program Files/IBM/SQLLIB/java/db2java.zip" />
    <properties resource="db.properties"></properties>
<!--    数据库连接-->
    <context id="DB2Tables" targetRuntime="MyBatis3">
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://127.0.0.1:3306/mybatis?useUnicode=true&amp;characterEncoding=utf-8&amp;useSSL=true&amp;serverTimezone=UTC"
                        userId="root"
                        password="123456">
        </jdbcConnection>

        <javaTypeResolver >
<!--java类型解析器，具体看文档-->
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>
<!--指定javaBean生成策略-->
        <javaModelGenerator targetPackage="Bean" targetProject="D:\Code\SSM_Work\MybatisMBG\src\main\webapp\Java">
<!--            targetPackage：目标包
                targetProject：目标工程
-->
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
<!--sql映射文件策略-->
        <sqlMapGenerator targetPackage="Mappers"
                         targetProject="D:\Code\SSM_Work\MybatisMBG\src\main\webapp\Resouorces">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>
<!--指定Mapper接口所在位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="Mappers"  targetProject="D:\Code\SSM_Work\MybatisMBG\src\main\webapp\Java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>
<!--指定逆向生成的数据库的表,根据表创建javaBean-->
       <table tableName="tbl_employee" domainObjectName="Employee"></table>
        <table tableName="tbl_depart" domainObjectName="Department"></table>
    </context>
</generatorConfiguration>
```

:sparkles:随后使用`Maven`中的插件，即可生成对应的Mapper和Model文件

:notebook:总结：MyBatis 逆向工程生成的Mapper仅仅是单表DAO操作

## 附录

[官方文档](http://www.mybatis.org/generator)

[IDEA中利用MyBatis Generator逆向工程](https://www.yisu.com/zixun/207459.html)

[MyBatis逆向工程笔记——CSDN](https://blog.csdn.net/eson_15/article/details/51694684)
