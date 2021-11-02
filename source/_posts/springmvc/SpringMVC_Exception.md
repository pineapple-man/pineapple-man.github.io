---
title: SpringMVC 异常处理流程
date: 2021-10-20 19:53:14
clearReading: true
thumbnailImage: https://gitee.com/mingchaohu/blog-image/raw/master/image/springMVC_Exception.png
thumbnailImagePosition: right
metaAlignment: center
tags: SpringMVC
keywords: SpringMVC
toc:  true
categories: Spring 全家桶
excerpt: 本文简单介绍 SpringMVC 中异常的使用方式
---
<!--toc-->

## 概述

:book:系统中异常包括：**编译时异常**和**运行时异常**(`RuntimeException`)，前者通过捕获异常从而获取异常信息，后者主要通过规范代码开发、通过测试手段减少运行时异常的发生

:sparkles:在开发中，不管是dao层、service层还是controller层，都有可能抛出异常，在springmvc中，能将所有类型的异常处理从各处理过程解耦出来，既保证了相关处理过程的功能较单一，也实现了异常信息的统一处理和维护

## 异常处理流程

下图是 Spring MVC 的异常处理流程

{% image fancybox  fig-100 center https://gitee.com/mingchaohu/blog-image/raw/master/image/springMVC_Exception.png %}

如上图所示，系统的dao、service、controller出现异常都通过throws Exception向上抛出，最后由springmvc前端控制器交由**异常处理器**进行异常处理

:sparkles:**springmvc提供全局异常处理器（一个系统只有一个异常处理器）进行统一异常处理**

## Spring MVC中自带的简单异常处理器

springmvc中自带了一个异常处理器叫SimpleMappingExceptionResolver，该处理器实现了HandlerExceptionResolver 接口，**全局异常处理都需要实现`HandlerExceptionResolver `接口**

```xml
<!-- springmvc提供的简单异常处理器 -->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
     <!-- 定义默认的异常处理页面 -->
    <property name="defaultErrorView" value="/WEB-INF/jsp/error.jsp"/>
    <!-- 定义异常处理页面用来获取异常信息的变量名，也可不定义，默认名为exception --> 
    <property name="exceptionAttribute" value="ex"/>
    <!-- 定义需要特殊处理的异常，这是重要点 --> 
    <property name="exceptionMappings">
        <props>
            <prop key="ssm.exception.CustomException">/WEB-INF/jsp/custom_error.jsp</prop>
        </props>
        <!-- 还可以定义其他的自定义异常 -->
    </property>
</bean>
```

:notes:进行配置时，最重要的是要配置特殊处理的异常，这些异常一般都是用户自定义的，并且不同的异常会促使页面跳转到不同的错误显示页面显示不同的错误信息

```java
//定义一个简单的异常类
public class CustomException extends Exception {

    //异常信息
    public String message;

    public CustomException(String message) {
        super(message);
        this.message = message;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

}
//随后通过 throw new CustomException(message);就会在系统触发异常，
//并被SimpleMappingExceptionResolver 捕获处理
```

:notebook:使用SimpleMappingExceptionResolver进行异常处理，具有**集成简单**、有**良好的扩展性**（可以任意增加自定义的异常和异常显示页面）、对已**有代码没有入侵性**等优点，但该方法**仅能获取到异常信息**，若想要在出现异常时，进行额外操作，`SimpleMappingExceptionResolver`不太合适

## 自定义全局异常处理器

:sailboat:全局异常处理器处理思路：

1. 解析出异常类型
2. 如果该异常类型是系统自定义的异常，直接取出异常信息，在错误页面展示
3. 如果该异常类型不是系统自定义的异常，构造一个自定义的异常类型（信息为“未知错误”）

```java
public class CustomExceptionResolver implements HandlerExceptionResolver {

    @Override
    public ModelAndView resolveException(HttpServletRequest request,HttpServletResponse response, Object handler, Exception ex) {

        ex.printStackTrace();
        CustomException customException = null;

        //如果抛出的是系统自定义的异常则直接转换
        if(ex instanceof CustomException) {
            customException = (CustomException) ex;
        } else {
            //如果抛出的不是系统自定义的异常则重新构造一个未知错误异常
            //这里再一次创建CustomException，实际中应该要再定义一个新的异常
            customException = new CustomException("系统未知错误");
        }

        //向前台返回错误信息
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("message", customException.getMessage());
        modelAndView.setViewName("/WEB-INF/jsp/error.jsp");

        return modelAndView;
    }
}
```

```xml
<!-- 自定义的全局异常处理器
只要实现HandlerExceptionResolver接口就是全局异常处理器-->
<bean class="ssm.exception.CustomExceptionResolver"></bean> 
```

## 注解实现异常管理

:sparkles:异常处理主要使用三个注解：`@ExceptionHandler`，`@ControllerAdvice`和`@RestControllerAdvice`

1. 使用`@ExceptionHandler`注解明确对特定的异常进行处理
2. `@ControllerAdvice`和`@RestControllerAdvice`将多个`@ExceptionHandler`聚集在一起统一进行异常管理

:notes:因为`@ExceptionHandler`注解的方式已经足够强大，所以很少通过实现`HandlerExceptionResolver`来自定义异常处理策略

### `@ExceptionHandler`

:sparkles:`@ExceptionHandler`注解特点

- 使用注解@ExceptionHandler可以将一个方法指定为异常处理方法。该注解只有一个可选属性`value`,类型是`Class<?>[]`，用于指定该注解的方法所要处理的异常类，即所要匹配的异常
- 被注解的方法，其返回值可以是ModelAndView、String，或void，方法名随意，方法参数可以是 Exception 及其子类对象、HttpServletRequest、HttpServletResponse 等。系统会自动为这些方法参数赋值

```java
@RestController
@RequestMapping("location")
public class LocationController {
    
 @RequestMapping("getLocationInfo")
    public String index() {
        int sum = 10 / 0;
        return "locationInfo";
    }
    //捕获 RuntimeException 异常
    @ExceptionHandler(RuntimeException.class)
    public String processRuntimeException() {
        return "LocationController -> 发生RuntimeException";
    }
    // 捕获 Exception 异常
     @ExceptionHandler(Exception.class)
    public String processException() {
        return "LocationController -> 发生Exception";
    }
}
```

如果在每个Controller里面都写异常解析器还是很麻烦的，可以使用`@RestControllerAdvice`或者`@ControllerAdvice`统一处理异常

### `@RestControllerAdvice`&`@ControllerAdvice`

:question:都能进行异常管理，@RestControllerAdvice和@ControllerAdvice有什么区别呢？

- @RestControllerAdvice是在@ControllerAdvice的基础上加了@ResponseBody注解
- @RestControllerAdvice返回的是JSON，@ControllerAdvice返回的是视图

```java
@RestControllerAdvice
public class MyExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public String processRuntimeException() {
        return "MyExceptionHandler -> 发生RuntimeException";
    }
    @ExceptionHandler(Exception.class)
    public String processException() {
        return "MyExceptionHandler -> 发生RuntimeException";
    }
}
```

:notes:使用注解进行统一异常处理的注意点

- 如果能够精确定义为异常发生点，则以精确异常为准
- 如果一个异常能被多个解析器所处理，则选择继承关系最近的解析器

## 附录

[SpringMVC中的统一异常处理](https://blog.csdn.net/eson_15/article/details/51731567)

[Spring MVC 异常解析器，原理就是这么简单](https://cloud.tencent.com/developer/article/1650039)

[SpringMVC 异常处理](https://segmentfault.com/a/1190000039333785)



