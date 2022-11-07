---
title: Spring核心注解
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: Spring
keywords: Spring注解
excerpt: 如今的 Spring 能够拥有这么多的使用者，主要原因是存在诸多注解，能够简化开发过程，本文将主要阐述，Spring 中存在哪些注解，并介绍它们都有什么用
date: 2022-01-21 10:53:45
thumbnailImage:
---

<!-- toc -->

本文是一篇总览性质的文章，用来总结当前 Spring 中用到的一些核心注解

|         注解         |                                       功能                                       |
| :------------------: | :------------------------------------------------------------------------------: |
|       `@Bean`        |                             向 Spring 容器中注册组件                             |
|      `@Primary`      |                          同类组件如果有多个，标注主组件                          |
|     `@DeoendsOn`     |                               组件之间生命依赖关系                               |
|       `@Lazy`        |                      组件懒加载（直到使用的时候才创建组件）                      |
|       `@Scope`       |             声明组件的作用范围（`SCOPE_PROTOTYPE`,`SCOPE_SINGLETON`)             |
|   `@Configuration`   |                      声明这是一个配置类，替换以前的配置文件                      |
|     `@Component`     |       声明这是一个组件，与(`@Controller`,`@Service`,`@Repository`)作用相同       |
|      `@Indexed`      |                 加速注解，所有标注了此注解的组件，会启动快速加载                 |
|       `@Order`       |                 声明组件加载优先级，数字越小优先级越高，优先工作                 |
|   `@ComponentScan`   |                                      包扫描                                      |
|    `@Conditional`    |                                     条件注入                                     |
|      `@Import`       |                导入第三方 jar 包中的组件，或定制批量导入组件逻辑                 |
|  `@ImportResource`   |                        导入以前的`xml`配置文件，让其生效                         |
|      `@Profile`      |                                  基于多环境激活                                  |
| `@PropertyResource`  | 外部`properties`配置文件和 JavaBean 进行绑定，进一步有`@ConfigurationProperties` |
| `@PropertyResources` |                            `@PropertySource`组合注解                             |
|     `@AutoWired`     |                                     自动装配                                     |
|     `@Qualifier`     |                                     精确指定                                     |
|       `@Value`       |                          取值、计算机环境变量、JVM 系统                          |
|      `@Lookup`       |                单例组件依赖非单例组件，非单例组件获取需要使用方法                |

:notes: `@Indexed` 注解想要使用的话，需要引入额外的 Spring 依赖

```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context-indexer</artifactId>
	<optional>true</optional>
</dependency>
```
