---
title: SWAGGER
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 中间件
tags: 中间件
keywords: 文档中间件
excerpt: 本文主要记录 swagger 中间件的特点
date: 2022-04-29 15:41:42
thumbnailImage:
---

<!-- toc -->

Swagger2 是一种文档中间件，方便前后端开发人员可以查看接口文档，为前后端开发人员的开发统一接口，方便后续的前后端联调对接工作

## 概述

使用 Swagger 只需要按照它的规范去定义接口及接口相关的信息。再通过 Swagger 衍生出来的一系列项目和工具，就可以做到生成各种格式的接口文档，生成多种语言的客户端和服务端的代码，以及在线接口调试页面等等。这样，如果按照新的开发模式，在开发新版本或者迭代版本的时候，只需要更新 Swagger 描述文件，就可以自动生成接口文档和客户端服务端代码，做到调用端代码、服务端代码以及接口文档的一致性。

为了简化 swagger 的使用，Spring 框架对 swagger 进行了整合，建立了 Spring-swagger 项目，后面改成了现在的 Springfox。通过在项目中引入 Springfox，可以扫描相关的代码，生成描述文件，进而生成与代码一致的接口文档和客户端代码。

Springfox 对应的 maven 坐标如下：

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
```

## swagger 常用注解

|         注解         |                           说明                            |
| :------------------: | :-------------------------------------------------------: |
|        `@Api`        |      用在请求的类上，例如 Controller，表示对类的说明      |
|     `@ApiModel`      |    用在类上，通常是实体类，表示一个返回响应数据的信息     |
| `@ApiModelProperty`  |               用在属性上，描述响应类的属性                |
|   `@ApiOperation`    |          用在请求的方法上，说明方法的用途、作用           |
| `@ApiImplicitParams` |            用在请求的方法上，表示一组参数说明             |
| `@ApiImplicitParam`  | 用在@ApiImplicitParams 注解中，指定一个请求参数的各个方面 |

## knife4j

knife4j 是为 Java MVC 框架集成 Swagger 生成 Api 文档的增强解决方案,前身是 swagger-bootstrap-ui,取名 knife4j 是希望它能像一把匕首一样小巧,轻量,并且功能强悍!其底层是对 Springfox 的封装，使用方式也和 Springfox 一致，只是对接口文档 UI 进行了优化

**核心功能**：

- **文档说明**：根据 Swagger 的规范说明，详细列出接口文档的说明，包括接口地址、类型、请求示例、请求参数、响应示例、响应参数、响应码等信息，对该接口的使用情况一目了然
- **在线调试**：提供在线接口联调的强大功能，自动解析当前接口参数,同时包含表单验证，调用参数可返回接口响应内容、headers、响应时间、响应状态码等信息，帮助开发者在线调试
