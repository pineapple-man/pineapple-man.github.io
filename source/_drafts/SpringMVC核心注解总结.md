---
title: SpringMVC核心注解总结
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: Java
tags: SpringMVC
keywords: SpringMVC
excerpt: 本文是一篇总结性文章，用于总结在开发中经常使用的SpirngMVC注解
---
## 概述

## `@Controller`与`@RestController`



## `@ResponseBody`

## `@RequestMapping` 注解

:sparkles:特点

- `@RequestMapping` 注解指定对应的 URL 将由那种控制器执行
- 标记在方法上：提供精确的映射
- 标记在类上：提供简单的映射关系，通常仍需将注解标记在方法上
- 若类上未标注`@RequestMapping`，则方法处标记的 URL 相对于 WEB 应用的根目录

:notes:DispatcherServlet 截获请求后，就通过控制器上 @RequestMapping 提供的映射信息确定请求所对应的处理方法

```java
@Controller
@RequestMapping(value = {"/hello"})
public class FirstController {
   @RequestMapping(value = {"/index"}, method = {RequestMethod.GET})
   public String index() {
      System.out.println("the page of hello had visited");
      return "success";
   }
}
```

:notes:需要访问`工程路径/hello/index`才能执行`index()`方法

##  `@RequestMapping`源码

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Mapping
public @interface RequestMapping {
   
    String name() default "";
	//请求URL限定
    @AliasFor("path")
    String[] value() default {};
    
    @AliasFor("value")
    String[] path() default {};
    //请求方法限定
	RequestMethod[] method() default {};
    //请求参数限定
    String[] params() default {};
    //请求头限定
    String[] headers() default {};
    //内容类型限定，规定请求头中的Content-Type字段
    String[] consumes() default {};
    //告诉浏览器返回的内容类型是什么，Content-Type
    String[] produces() default {};
}
```

如果设定了多个参数，需要同时满足，才会执行对应的控制器，做到请求映射的精确控制

## 限定处理特定请求参数

```java
@Controller
@RequestMapping(value = {"/hello"})
public class FirstController {
   @RequestMapping(value = {"/index"}, method = {RequestMethod.GET}, params = {"username", "!password", "id=001", "gender!=male"})
   public String index() {
      System.out.println("the page of hello had visited");
      return "success";
   }
}
```

以下`url`可以成功执行`index()`方法

```http
GET http://localhost:8080/hello/index?id=002&username=pineapple-man&gender=female
```

## 限定处理特定请求头

做到只有谷歌浏览器可以访问，其他浏览器均不能访问

```java
@RequestMapping(value = {"/index"}, method = {RequestMethod.GET}, headers = {"User-Agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.72 Safari/537.36"})
public String index() {
	System.out.println("the page of hello had visited");
	return "success";
}
```

## `@PathVariable`注解

:notes: `@PathVariable`注解含义

- 映射 URL 绑定的占位符（在路径的任意地声明变量）
- 通过`@PathVariable`可以将 URL 中占位符参数绑定到控制器处理方法的入参中

> URL 中的 `{xxx}`占位符可以通过`@PathVariable("xxx")`绑定到操作方法的入参中

```java
@Controller
@RequestMapping(value = {"/hello"})
public class FirstController {
	@RequestMapping(value = {"/{index?}", "/index/*/?"}, method = {RequestMethod.GET})
	public String index(@PathVariable("index?") String index) {
		System.out.println("index = " + index);
		System.out.println("the page of hello had visited");
		return "success";
	}
}
```

:notes:访问`/hello/index`、`/hello/index1`、`/hello/indexa`均能得到响应

## 获取请求参数注解

SpringMVC 获取请求参数的两种方式：

- 默认方式
- 使用注解获取

## 默认方式

:sparkles:直接给方法入参上写一个和请求参数名相同的变量，这个变量就来接收请求参数的值

如果请求中带有此值，则能截获；如果请求中不带有此参数，默认为`null`

```java
@RequestMapping(value = {"/users", "/users/*"}, method = {RequestMethod.GET})
public String getMethod(String username) {
   System.out.println("username is " + username);
   System.out.println("get");
   return "success";
}
```

```http
http://localhost:8080/users?username=1
```

## `@RequestParam`注解

用于获取请求参数，在处理方法入参时使用，可以把请求参数传递给请求方法

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface RequestParam {
	@AliasFor("name")
	String value() default "";

	@AliasFor("value")
	String name() default "";

	boolean required() default true;

	String defaultValue() default ValueConstants.DEFAULT_NONE;
}
```

##  `@RequestHeader`

:notes:获取请求头中某个 key 的value，如果请求头中没有这个值就会报错

可将请求头中的属性值绑定到处理方法的入参中

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface RequestHeader {

   @AliasFor("name")
   String value() default "";

   @AliasFor("value")
   String name() default "";

   boolean required() default true;

   String defaultValue() default ValueConstants.DEFAULT_NONE;

}
```

## `@CookieValue`

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface CookieValue {
	@AliasFor("name")
	String value() default "";

	@AliasFor("value")
	String name() default "";

	boolean required() default true;
    
	String defaultValue() default ValueConstants.DEFAULT_NONE;

}
```

## Demo

```java
@RequestMapping(value = {"/users", "/users/*"}, method = {RequestMethod.GET})
public String getMethod(@RequestParam(value = "username", required = false) String username,
                        @RequestHeader(value = "User-Agent", required = false) String userAgent,
                        @CookieValue(value = "JSESSIONID", required = false) String cookie
) {
   System.out.println("username is " + username);
   System.out.println("user-Agent is " + userAgent);
   System.out.println("JSESSION ID is " + cookie);
   System.out.println("get");
   return "success";
}
```



## 总结

:notes:提交的数据可能有乱码

- 请求乱码
  - GET请求：改server.xml；在8080端口处URIEncoding="UTF-8"
  - POST请求： 在第一次获取请求参数之前设置，request.setCharacterEncoding("UTF-8");
- 响应乱码
  - response.setContentType("text/html;charset=utf-8")



## `@SessionAttributes`

### 源码

```java
@Target({ElementType.TYPE}) 
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface SessionAttributes { 
String[] value() default {};   
Class<?>[] types() default {};  
}
```

```java
//会将隐含模型中所有类型为 User.class 的属性添加到会话中
@SessionAttributes(types=User.class)
@SessionAttributes(value={“user1”, “user2”})
@SessionAttributes(types={User.class, Dept.class})
@SessionAttributes(value={“user1”, “user2”}, types={Dept.class}) 
```







## `@RestController`和`@Controller`



## `@RestController`注解

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Controller
@ResponseBody
public @interface RestController {

   @AliasFor(annotation = Controller.class)
   String value() default "";

}
```



## `@Controller`注解

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {

   @AliasFor(annotation = Component.class)
   String value() default "";

}
```

:notes:`@RestController`注解比`@Controller`注解多上`@ResponseBody`注解



## `@ResponseBody`注解

@ResponseBody表示返回结果是json，与返回ModelAndView相对，一般用于后端的api服务。

@Responsebody 表示该方法的返回结果直接写入 HTTP response body 中

https://www.cnblogs.com/guodefu909/p/4216327.html

https://blog.csdn.net/qq_35246620/article/details/59620858

https://blog.csdn.net/zssy666/article/details/79134819

https://blog.csdn.net/u010900754/article/details/99110569

https://blog.csdn.net/u010900754/article/details/105331015







## 附录

[Spring @Controller and @RestController Annotations](https://howtodoinjava.com/spring-boot2/rest/controller-restcontroller/)

[Spring REST JSON Response Example](https://howtodoinjava.com/spring-rest/spring-rest-hello-world-json-example/)

