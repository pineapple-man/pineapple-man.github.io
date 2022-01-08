---
title: Maven 踩坑指南
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-01-08 20:19:08
thumbnailImage:
categories: Java
tags: 项目
keywords: Maven
excerpt: 本文主要记录使用 Maven 的过程中遇到的问题
---
<!-- toc -->
## Maven打包web项目报错Error assembling WAR: webxml attribute is required (or pre-existing WEB-INF/web.xml 

{% alert danger no-icon %}

:bug:maven的web项目默认的webroot是在src\main\webapp，如果在此目录下找不到web.xml就抛出以上的异常
{% endalert %}

:bulb:解决方法[^1]

{% alert success no-icon %}
在`pom.xml`文件中指定`web.xml`位置，具体的配置如下：
{% endalert %}

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <version>2.2</version>
    <configuration>
        <webXml>WEB-INF/web.xml</webXml>
    </configuration>
</plugin>
```

## idea 卡在Resolving Maven dependencies
:bulb:解决方法[^2]
{% alert success no-icon %}
增加`maven importing`的 JVM 参数`-Xms1024m -Xmx2048m`，具体的配置如下：
{% endalert %}

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/maven/maven-config-parameters.png %}



## 附录
[^1]: https://blog.csdn.net/pange1991/article/details/48596869
[^2]: https://blog.csdn.net/jiangyu1013/article/details/95042611