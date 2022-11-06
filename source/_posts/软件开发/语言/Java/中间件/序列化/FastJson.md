---
title: FastJson
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: 中间件
tags: Web
keywords: FastJson
excerpt: 本文主要记录 FastJson 工具的常见使用方式
date: 2021-11-14 21:54:20
thumbnailImage:
---

<!-- toc -->

{% hl_text red %}
FastJson 也常常由于其的安全性能而被诟病（它的 BUG 真的太多了），所以不是非常推荐使用
{% endhl_text %}

# 概述

FastJson 是阿里巴巴的开源 JSON 解析库,它可以解析 JSON 格式的字符串，支持将 Java Bean 序列化为 JSON 字符串，也可以从 JSON 字符串反序列化到 JavaBean

:+1:Fastjson 的优点
{% alert success no-icon%}

速度快、使用广泛、使用简单以及功能完备（支持泛型、支持流处理超大文本、支持枚举、支持序列化和反序列化扩展）

{%endalert%}

## 序列化 API

序列化是指将 Java 对象转成 json 格式字符串的过程。其中 JavaBean 对象，List 集合对象，Map 集合，是序列化时应用最广泛的对象。`JSON.toJSONString` 是常用的序列化 API，使用方式如下

### 序列化 Java 对象

```java
public void objectToJson(){
    Student student = new Student();
    student.setId(1);
    student.setName("张三");
    student.setAge(20);
    student.setAddress("北京市");
    student.setEmail("zs@sina.com");
    String jsonString = JSON.toJSONString(student);
    System.out.println(jsonString);
}
```

### 序列化 List 集合

```java
public void listToJson(){
    Student student = new Student();
    student.setId(1);
    student.setName("张三");
    student.setAge(20);
    student.setAddress("北京市");
    student.setEmail("zs@sina.com");

    Student student2 = new Student();
    student2.setId(2);
    student2.setName("张三2");
    student2.setAge(22);
    student2.setAddress("北京市2");
    student2.setEmail("zs2@sina.com");

    List<Student> list = new ArrayList<Student>();
    list.add(student);
    list.add(student2);
    String jsonString = JSON.toJSONString(list);
    System.out.println(jsonString);
}
```

### 序列化 Map 集合

```java
public void mapToJson(){
    Student student = new Student();
    student.setId(1);
    student.setName("张三");
    student.setAge(20);
    student.setAddress("北京市");
    student.setEmail("zs@sina.com");

    Student student2 = new Student();
    student2.setId(2);
    student2.setName("张三2");
    student2.setAge(22);
    student2.setAddress("北京市2");
    student2.setEmail("zs2@sina.com");
    Map<String,Student> map = new HashMap<String, Student>();
    map.put("s1",student);
    map.put("s2",student2);
    String jsonString = JSON.toJSONString(map);
    System.out.println(jsonString);
}
```

## FashJson 反序列化 API

反序列化是序列化的反向操作，经常使用的 API 如下：

### 反序列化 Java 对象

`JSON.parseObject` API 能够对 Java 对象进行反序列化操作，也能够对 Map 对象进行反序列化操作

```java
public void jsonToObject(){
    String jsonString = "{\"address\":\"北京市\",\"age\":20,\"email\":\"zs@sina.com\",\"id\":1,\"name\":\"张三\"}";
    Student student = JSON.parseObject(jsonString, Student.class);
    System.out.println(student);
}
```

### 反序列化 Map 集合

```java
 public void jsonToMap(){
     String jsonString = "{\"s1\":{\"address\":\"北京市\",\"age\":20,\"email\":\"zs@sina.com\",\"id\":1,\"name\":\"张三\"},\"s2\":{\"address\":\"北京市2\",\"age\":22,\"email\":\"zs2@sina.com\",\"id\":2,\"name\":\"张三2\"}}";
     Map<String,Student> parse = JSON.parseObject(jsonString,new TypeReference<Map<String,Student>>(){});

     for(String s : parse.keySet()){
     	System.out.println(s + ":::"+parse.get(s));
     }
 }
```

### 反序列化 List 集合

`JSON.parseArray` 是常用的对 List 集合进行反序列化操作

```java
public void jsonToList(){
    String jsonString = "[{\"address\":\"北京市\",\"age\":20,\"email\":\"zs@sina.com\",\"id\":1,\"name\":\"张三\"},{\"address\":\"北京市2\",\"age\":22,\"email\":\"zs2@sina.com\",\"id\":2,\"name\":\"张三2\"}]";
    List<Student> list = JSON.parseArray(jsonString,Student.class);
    for (int i = 0; i < list.size(); i++) {
        Student student =  list.get(i);
        System.out.println(student);
    }
}
```

## @JSonField 注解

该注解可作用于方法上,字段上和参数上，可在序列化和反序列化时进行特性功能定制

- 注解属性 : name 序列化后的名字
- 注解属性 : ordinal 序列化后的顺序
- 注解属性 : format 序列化后的格式
- 注解属性 : serialize 是否序列化该字段
- 注解属性 : deserialize 是否反序列化该字段
- 注解属性 : serialzeFeatures 序列化时的特性定义

## @ JSonType 注解

该注解作用于类上，用于对该类的字段进行序列化和反序列化时的特性功能定制

- 注解属性 : includes 要被序列化的字段
- 注解属性 : orders 序列化后的顺序
- 注解属性 : serialzeFeatures 序列化时的特性定义
