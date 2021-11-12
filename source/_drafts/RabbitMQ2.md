---
title: RabbitMQ 简单操作
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: 中间件
tags:
    - MQ
keywords: 
    - RabbitMQ 
    - 消息队列
excerpt: 本文主要讲解使用 Java API 如何简单的操作RabbitMQ
---

{% codeblock pom.xml lang:xml %}
        <!--rabbitmq 依赖客户端-->
        <dependency>
            <groupId>com.rabbitmq</groupId>
            <artifactId>amqp-client</artifactId>
            <version>5.8.0</version>
        </dependency>
        <!--操作文件流的一个依赖-->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.6</version>
        </dependency>
{% endcodeblock %}
