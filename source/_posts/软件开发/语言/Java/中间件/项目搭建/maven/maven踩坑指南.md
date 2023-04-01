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

## Maven 打包 web 项目报错 Error assembling WAR: webxml attribute is required (or pre-existing WEB-INF/web.xml

{% alert danger no-icon %}

:bug:maven 的 web 项目默认的 webroot 是在 src\main\webapp，如果在此目录下找不到 web.xml 就抛出以上的异常
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

## idea 卡在 Resolving Maven dependencies

:bulb:解决方法[^2]
{% alert success no-icon %}
增加`maven importing`的 JVM 参数`-Xms1024m -Xmx2048m`，具体的配置如下：
{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/maven/maven-config-parameters.png %}

## Github 下载多模块项目，构建失败

一个合理的 git 仓库提交后的内容是不应该存在 idea 种`*.iml`文件的，但是这样如果是多模块项目，拉到本地；相对于正常的模块项目，模块并不会自动识别，此时需要手动配置成多模块项目
:one: 导入模块
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/maven/maven-import.png %}
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/maven/mave-module-import.png %}
:two: 从外部模型导入模块
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/maven/maven-from-outside-import.png %}

<!-- 通过以上两步即完成了多模块项目的搭建，之后会自动生成`iml`文件 -->

## 附录

[^1]: https://blog.csdn.net/pange1991/article/details/48596869
[^2]: https://blog.csdn.net/jiangyu1013/article/details/95042611

[构建 Maven 多模块项目](https://zhuanlan.zhihu.com/p/84175296)
[导入模块生成.iml 模块描述文件](https://blog.csdn.net/u010003835/article/details/84101041)
