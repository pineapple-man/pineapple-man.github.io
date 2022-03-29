---
title: Spring IoC
toc: true
date: 2021-10-23 23:55:39
clearReading: true
metaAlignment: center
categories: Java
tags: Spring
keywords: 
    - Spring IOC
    - 自动注入
excerpt: 本文主要介绍 Spring 中的IoC容器使用方法（XML配置方式以及注解方式）
---
<!-- toc -->
## Spring IoC

Spring 提供 IoC 容器实现两种方式（两个接口）：

- `BeanFactory`
   IoC 容器基本实现，是 Spring **内部使用接口**
   不提倡开发人员进行使用
 . 加载配置文件时不会创建对象，获取（使用）对象时才会创建对象
- `ApplicationContext`
   `BeanFactory`的子接口
   推荐开发人员使用
 . 加载配置文件时，就会将配置文件中的对象进行创建

:question:为什么读取配置文件时，就创建对象？

- 服务端，为了能够更快速高效的为用户提供服务；需要使用的对象，最好启动服务时就创建，以免出现请求时，对象才开始创建的现象

### ApplicationContext 主要实现类

|             实现类              |               区别                |
| :-----------------------------: | :-------------------------------: |
| ClassPathXmlApplicationContext  | 读取类路径下的 XML 格式的配置文件 |
| FileSystemXmlApplicationContext |  文件系统中的 XML 格式的配置文件  |

![ApplicationContext继承体系](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210607182210091.png)
### ConfigurableApplicationContext

:sparkles:`ConfigurableApplicationContext`类

- 是`ApplicationContext`的子接口
- `refresh()`和`close()`让`ApplicationContext`具有启动、关闭和刷新上下文的能力

### WebApplicationContext

:sparkles:专门为 WEB 应用而准备，允许从相对于 WEB 根目录的路径中完成初始化工作

## Bean 管理

:sparkles:IoC 中进行 Bean 管理，主要指的时两个操作：

- Spring <font style="color:red;font-weight:bold">创建 Bean 对象</font>
- Spring <font style="color:red;font-weight:bold">注入属性</font>

Bean 管理操作存在两种方式：

- 基于 XML 配置文件方式实现（新手）
- 基于注解方式实现（老鸟）

## XML 方式创建 Bean 对象

Spring 项目的构建至少需要如下几个包

- spring-beans
- spring-context
- spring-core
- spring-expression

### 创建 Java Bean

```java
public class User {
 private String name;
 private Integer age;
 
 public void add() {
  System.out.println("from User's add method...");
 }
}
```

### Spring 配置文件

```xml
<?xml version="0" encoding="UTF-?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www..org/0XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="user" class="com.pineapple_man.spring.User"></bean>
</beans>
```

:sparkles:Spring 配置文件特点

- 使用`<bean>`标签，实现对象创建
- `id`属性：唯一标识
- `class`属性：创建类的全路径

:notes:Spring 创建对象时，默认执行无参数构造方法完成对象创建

### 代码测试

```java
public class UserTest {
 @Test
 public void add() {
  ApplicationContext applicationContext = new ClassPathXmlApplicationContext("beanxml");
        //getBean()的第一个参数是 beanxml 中 bean 的 id
  User user = applicationContext.getBean("user", User.class);
  user.add();
 }
}
```

## XML 方式注入属性

对于大项目通常使用注解方式，XML 方式使得项目复杂并且难以管理，但是便于学习

![XML方式注入属性](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/XML方式注入属性.png)

### set 方法注入

 创建类，定义属性

```java
public class User {
 private String name;
 private Integer age;
 
 public void add() {
  System.out.println("from User's add method...");
 }
 
 public void setName(String name) {
  this.name = name;
 }
 
 public void setAge(Integer age) {
  this.age = age;
 }
 
 @Override
 public String toString() {
  return "User{" +
    "name='" + name + '\'' +
    ", age=" + age +
    '}';
 }
}
```

 在 Spring 配置文件配置对象创建，配置属性注入

```xml
<bean id="user" class="com.pineapple_man.spring.User">
 <property name="age" value=""></property>
 <property name="name" value="张三丰"></property>
</bean>
```

:notes:`<property>`标签中属性值含义

- `name`类里面属性名称
- `value`向属性注入的值

### 有参构造进行注入

 创建有参数构造

```java
public class User {
 private String name;
 private Integer age;
 
 public User(String name, Integer age) {
  this.name = name;
  this.age = age;
 }
 
 @Override
 public String toString() {
  return "User{" +
    "name='" + name + '\'' +
    ", age=" + age +
    '}';
 }
}
```

 在 Spring 配置文件中进行配置

```xml
<bean id="user" class="com.pineapple_man.spring.User">
 <constructor-arg name="age" value=""></constructor-arg>
 <constructor-arg name="name" value="达摩老祖"></constructor-arg>
</bean>
```

### p 名称空间注入

 添加 p 名称空间到配置文件中

```xml
<?xml version="0" encoding="UTF-?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.worg/0XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

 进行属性注入

```xml
<bean id="user" class="com.pineapple_man.spring.User" p:age=" p:name="超哥"></bean>
```

## XML 方式注入特殊值

主要是通过 xml 注入其他类型的数据

### 注入 null

```xml
<bean id="user" class="com.pineapple_man.spring.User">
 <property name="age" value=""></property>
 <property name="name">
  <null></null>
 </property>
</bean>
```

### 注入属性包含特殊符号

存在两种方式:

- 使用转移字符(`&lt`,`&gt`)
- 将带特殊符号内容写到 CDATA 中

```xml
<bean id="user" class="com.pineapple_man.spring.User">
 <property name="age" value=""></property>
 <property name="name">
  <value><![CDATA[<<南京>>]]></value>
 </property>
</bean>
```

### 注入外部 bean

 在`Service`类的内部，调用了`dao`类方法

```java
public class UserService {
 UserDao dao = new UserImpl();
 
 public void setDao(UserDao dao) {
  this.dao = dao;
 }
 
 public void update() {
  System.out.println("from service update method.");
  dao.update();
 }
}
```

```java
public class UserImpl implements UserDao {
 @Override
 public void update() {
  System.out.println("from userimple update method");
 }
}
```

 配置文件中进行配置

```xml
<bean id="userService" class="com.pineapple_man.spring.service.UserService">
 <property name="dao" ref="userDao"/>
</bean>
<bean id="userDao" class="com.pineapple_man.spring.dao.impl.UserImpl"></bean>
```

:sparkles:`ref`属性：只想另一个`javaBean`的`id`

### 注入内部 bean

 书写对应实体类

```java
public class Class {
 private String name;
 private int stuentsNumber;
 
 public void setName(String name) {
  this.name = name;
 }
 
 public void setStuentsNumber(int stuentsNumber) {
  this.stuentsNumber = stuentsNumber;
 }
 
 @Override
 public String toString() {
  return "Class{" +
    "name='" + name + '\'' +
    ", stuentsNumber=" + stuentsNumber +
    '}';
 }
}
```

```java
public class Student {
 private String name;
 private Integer age;
 private String gender;
 //一个学生属于一个班级
 private Class aClass;
 //内部注入，不需要getter方法
 public void setaClass(Class aClass) {
  this.aClass = aClass;
 }
 
 @Override
 public String toString() {
  return "Student{" +
    "name='" + name + '\'' +
    ", age=" + age +
    ", gender='" + gender + '\'' +
    '}';
 }
 
 public void setName(String name) {
  this.name = name;
 }
 
 public void setAge(Integer age) {
  this.age = age;
 }
 
 public void setGender(String gender) {
  this.gender = gender;
 }
 
 public Student(String name, Integer age, String gender) {
  this.name = name;
  this.age = age;
  this.gender = gender;
 }
}
```

 内部 bean 配置

```xml
<bean id="student" class="com.pineapple_man.spring.bean.Student">
 <constructor-arg name="name" value="Tony"></constructor-arg>
 <constructor-arg name="age" value=""></constructor-arg>
 <constructor-arg name="gender" value="male"></constructor-arg>
 <property name="aClass">
  <bean id="class" class="com.pineapple_man.spring.bean.Class">
   <property name="name" value="S00"></property>
   <property name="stuentsNumber" value=""></property>
  </bean>
 </property>
</bean>
```

### 级联赋值

级联赋值，用于向一个对象中注入另一个对象

#### 注入外部 bean 方式

```xml
<bean id="student" class="com.pineapple_man.spring.bean.Student">
 <constructor-arg name="name" value="Tony"></constructor-arg>
 <constructor-arg name="age" value=""></constructor-arg>
 <constructor-arg name="gender" value="male"></constructor-arg>
 <!--级联赋值-->
    <property name="aClass" ref="class"></property>
</bean>
<bean id="class" class="com.pineapple_man.spring.bean.Class">
 <property name="name" value="S00"></property>
 <property name="stuentsNumber" value=""></property>
</bean>
```

#### 指定属性注入

```xml
<bean id="student" class="com.pineapple_man.spring.bean.Student">
 <constructor-arg name="name" value="Tony"></constructor-arg>
 <constructor-arg name="age" value=""></constructor-arg>
 <constructor-arg name="gender" value="male"></constructor-arg>
 <!--级联赋值-->
    <property name="aClass" ref="class"></property>
 <property name="aClass.name" value="S00"/>
</bean>
<bean id="class" class="com.pineapple_man.spring.bean.Class">
</bean>
```

:notes:`ref`指定的类需要在当前类中存在`setter`和`getter`方法

### 注入集合属性

对集合类属性注入

#### 常见类型注入

 创建包含集合属性的类，拥有数组、`List`、`Map`、`Set`属性

```java
public class Student {
 private String name;
 private Integer age;
 private String gender;
 private String[] courses;
 private List<String> list;
 private Map<String, String> map;
 private Set<String> set;
 
 public void setSet(Set<String> set) {
  this.set = set;
 }
 
 public void setCourses(String[] courses) {
  this.courses = courses;
 }
 
 public void setList(List<String> list) {
  this.list = list;
 }
 
 public void setMap(Map<String, String> map) {
  this.map = map;
 }
 public Student() {
 }
}
```

 配置文件

```xml
<bean id="student" class="com.pineapple_man.spring.bean.Student">
    <property name="courses">
        <array>
            <value>语文</value>
            <value>数学</value>
            <value>英语</value>
        </array>
    </property>
    <property name="list">
        <list>
            <value>语文</value>
            <value>数学</value>
            <value>英语</value>
        </list>
    </property>
    <property name="set">
        <set>
            <value>MySQL</value>
            <value>ELK</value>
        </set>
    </property>
    <property name="score">
        <map>
            <entry key="语文" value=""></entry>
            <entry key="数学" value="0"></entry>
        </map>
    </property>
</bean>
```

#### bean 类型注入

```xml
<bean id="user class="com.pineapple_man.spring.bean.User">
 <property name="name" value="超哥"></property>
 <property name="age" value=""></property>
</bean>
<bean id="user class="com.pineapple_man.spring.bean.User">
 <property name="name" value="龙哥"></property>
 <property name="age" value=""></property>
</bean>
<bean id="student" class="com.pineapple_man.spring.bean.Student">
 <!--注入list集合类型，值是对象-->
    <property name="teachers">
     <list>
         <ref bean="user></ref>
            <ref bean="user></ref>
  </list>
 </property>
</bean>
```

```java
public class Student {
   private List<User> teachers;
   
   public void setTeachers(List<User> teachers) {
      this.teachers = teachers;
   }
}
```

#### 提取共同代码

 在 Spring 配置文件中引入名称空间`util`

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.worg/0XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">
</beans>
```

 使用`utils`标签完成 list 集合公共部分的提取

```xml
<bean id="user class="com.pineapple_man.spring.bean.User">
 <property name="name" value="龙哥"></property>
</bean>
<bean id="user class="com.pineapple_man.spring.bean.User">
 <property name="name" value="超哥"></property>
</bean>
<util:list id="teacherList">
 <ref bean="user></ref>
    <ref bean="user></ref>
</util:list>
<bean id="stu class="com.pineapple_man.spring.bean.Student">
 <property name="teachers" ref="teacherList"></property>
</bean>
```

## FactoryBean

:sparkles:Spring 有两种类型`bean`：普通`bean`和`FactoryBean`

- 普通bean：配置文件中定义`bean`类型就是返回类型
- 工厂bean：配置文件定义`bean`类型可以和返回类型不同

:sailboat:FactoryBean 使用步骤：

 1. 创建工厂bean类，实现`FactoryBean`接口
 2. 实现接口中的方法，在实现的方法中返回声明的 bean 类型

```java
public class MyBean implements FactoryBean<User> {
    //定义返回的 bean 类型
 @Override
 public User getObject() throws Exception {
  return new User("龙哥", );
 }
 
 @Override
 public java.lang.Class<?> getObjectType() {
  return null;
 }
 
 @Override
 public boolean isSingleton() {
  return FactoryBean.super.isSingleton();
 }
}
```

3. 书写配置文件

```xml
<bean id="mybean" class="com.pineapple_man.spring.bean.MyBean"></bean>
```

4. 检查结果

```java
public void beanTest() {
 ApplicationContext applicationContext = new ClassPathXmlApplicationContext("beanxml");
 User mybean= applicationContext.getBean("mybean", User.class);
 User mybean= applicationContext.getBean("mybean", User.class);
 System.out.println(mybeangetClass().hashCode());
 System.out.println(mybeangetClass().hashCode());
    //mybean与 mybean的hash值相同
}
```

## Bean 作用域

默认情况下，Spring 中 bean 是单实例对象，通过修改配置文件，可以选择是单实例还是多实例

`scope`属性有两个值：`singleton`和`prototype`

```xml
<bean id="mybean" class="com.pineapple_man.spring.bean.MyBean" scope="prototype"></bean>
```

:sparkles:`singleton`和`prototype`的区别

- `singleton`是单实例，`prototype`是多实例
- scope 为`singleton`时，**加载配置文件就会创建单实例对象**
- scope 为`prototype`时，调用`getnBean()`方法时**创建多实例对象**

## bean 生命周期

:sparkles: bean 的生命周期（七个阶段）：

 通过构造器创建`bean`实例（无参数构造）
 为`bean`的属性注入以及对其他`bean`引用(调用`set()`方法)
3. 将`bean`实例传递给`bean`后置处理器的方法`postProcessBeforeInitialization`
4. 调用`bean`的初始化方法（需要进行配置初始化的方法）
5. 将`bean`实例传递给`bean`后置处理器方法`postProcessAfterInitialization`
 使用`bean`
 在容器关闭时，调用`bean`的销毁方法（需要配置销毁方法）

### Java Bean

```java
public class LifeCycle {
   private String step;
   
   public LifeCycle() {
      System.out.println(" 来自于无参构造函数");
   }
   
   public void setStep(String step) {
      System.out.println(" 来自于set方法");
      this.step = step;
   }
   
   public void initMethod() {
      System.out.println(. 来自于initMethod");
   }
   
   public void destoryMethod() {
      System.out.println("5.来自destoryMethod");
   }
}
```

### 预处理和后处理类

```java
public class PostProcess implements BeanPostProcessor {
 @Override
 public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
  System.out.println("from postProcessBeforeInitialization");
  return BeanPostProcessor.super.postProcessBeforeInitialization(bean, beanName);
 }
 
 @Override
 public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
  System.out.println("from postProcessAfterInitialization");
  return BeanPostProcessor.super.postProcessAfterInitialization(bean, beanName);
 }
}
```

### Spring 配置

```xml
<?xml version="0" encoding="UTF-?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www..org/0XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="lifecycle" class="com.pineapple_man.spring.bean.LifeCycle" init-method="initMethod"
          destroy-method="destoryMethod">
        <property name="step" value="start"></property>
    </bean>
    <bean id="postprocess" class="com.pineapple_man.spring.bean.PostProcess"></bean>
</beans>
```

:notes:预处理和后处理将会作用在此文件的所有 bean 对象

## xml 自动装配

:sparkles:自动状态就是——根据指定装配规则（属性名称或者属性类型），Spring 自动将匹配的属性值进行注入

```java
//Java Bean
public class AutoInject {
 private User user;
 private User teacher;
 
 public AutoInject() {
 }
 
 @Override
 public String toString() {
  return "AutoInject{" +
    "user=" + user +
    ", teacher=" + teacher +
    '}';
 }
 
 public void setTeacher(User teacher) {
  this.teacher = teacher;
 }
 
 public void setUser(User user) {
  this.user = user;
 }
}
```

### 根据属性名称自动注入

:notes:注入值 bean 的 id 值和类属性名称一样

```xml
<bean class="com.pineapple_man.spring.bean.AutoInject" id="autoInject" autowire="byName">
</bean>
<bean class="com.pineapple_man.spring.bean.User" id="user">
    <property name="name" value="tony"></property>
    <property name="age" value=""></property>
</bean>
<bean class="com.pineapple_man.spring.bean.User" id="teacher">
    <property name="age" value=""></property>
    <property name="name" value="Dany"></property>
</bean>
```

### 根据属性类型自动注入

:notes:类型自动注入只能应用在类中只有这样一种类型的属性，否则可能找不到注入属性

```xml
<bean class="com.pineapple_man.spring.bean.AutoInject" id="autoInject" autowire="byType">
</bean>
<bean class="com.pineapple_man.spring.bean.User" id="user">
 <property name="name" value="tony"></property>
 <property name="age" value=""></property>
</bean>
<bean class="com.pineapple_man.spring.bean.User" id="teacher">
 <property name="age" value=""></property>
 <property name="name" value="Dany"></property>
</bean>
```

## 外部属性文件

将外部属性文件内的数据，导入到 Spring 配置文件中

### 直接配置数据库信息

```xml
<bean class="com.alibaba.druid.pool.DruidDataSource" id="dataSource">
 <property name="name" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:///t_user"></property>
    <property name="username" value="root"></property>
    <property name="password" value=45></property>
</bean>
```

```java
public void druidTest) {
 ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean.xml");
 DruidDataSource dataSource = applicationContext.getBean("dataSource", DruidDataSource.class);
 System.out.println(dataSource.getPassword());
 System.out.println(dataSource.getUsername());
}
```

### 外部属性文件配置数据库连接池

#### 增加 jdbc.properties 文件，保存数据库信息

```properties
prop.driverClass=com.mysql.jdbc.Driver
prop.url=jdbc:mysql:///
prop.userName=root
prop.password456
```

#### Spring 配置文件

```xml
<?xml version="0" encoding="UTF-?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www..org/0XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!--引入外部文件-->
    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
    <bean class="com.alibaba.druid.pool.DruidDataSource" id="dataSource">
        <property name="name" value="${prop.driverClass}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.userName}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>
</beans>
```

## 注解方式管理 Bean

:sparkles:注解特点

- 注解是特殊标记，格式`@注解名称(属性名称=属性值,...)`
- 注解作用在类上面，方法上面，属性上面

:+使用注解方式管理 bean 的优点

- 简化 xml 配置
- 便于程序阅读

### 可用注解

> :notes:Sping 创建对象的四个注解：
>
> `@Component`
> `@Service`
>. `@Controller`
> 4. `@Repository`

:notes:上面四个注解功能是一样的，都是可以用来创建 bean 实例

### .基于注解方式实现对象创建

主要共分为三个步骤

#### 引入依赖

在项目中引入`spring-aop-5.RELEASE.jar`包

#### 组件扫描配置

在 Spring 配置文件中进行组件扫描配置

```xml
<?xml version="0" encoding="UTF-?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www..org/0XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com.pineapple_man.spring.aop"></context:component-scan>
</beans>
```

:notes:组件扫描注意点

- 如果扫描多个包，多个包之间使用逗号隔开
- 如果一个目录中有多个类进行扫描，则配置自动扫描上层目录

#### Spring 托管创建实例

书写 Java Bean，在类上添加创建对象注解

```java
@Component(value = "aopUserService")
public class AopUserService {
 public void method() {
  System.out.println("from AopUserService method");
 }
}
```

:notes:`@Component(value)`中的`value`属性是可以省略不写的，默认值是：**首字母小写的类名**，如果书写了，value 等价与`<bean>`中的 id 属性

### 组件扫描详细配置
实现扫描过滤器配置，可以选择在包中，默认扫描那些类，不扫描那些类

```xml
<?xml version="0" encoding="UTF-?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www..org/0XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com.pineapple_man.spring.aop"
                            use-default-filters="false">
  <!--默认仅扫描拥有 @Component 注解的类-->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Component"/>
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Component"/>
    </context:component-scan>
</beans>
```

:sparkles:扫描过滤器特点

 `use-default-filters="false"`：表示不适用默认过滤器，用户自己配置过滤器
 `<context:include-filter type="" expression="">`：设置那些内容进行扫描
`<context:exclude-filter type="" expression="">`：设置那些内容不进行扫描

## 注解方式实现属性注入

|     注解     |                     含义                     |
| :----------: | :------------------------------------------: |
| `@Autowired` |           根据属性类型进行自动装配           |
| `@Qualifier` | 根据名称进行注入，需要和`@Autowired`一起使用 |
| `@Resource`  |      可以根据类型注入也可以根据名称注入      |
|   `@Value`   |               注入普通类型属性               |

```java
@Repository(value = "aopDao")
public class AopDao {
 public void method() {
  System.out.println("From aopDao method");
 }
}

```

### @Autowired

```java
@Component(value = "aopUserService")
public class AopUserService {
 @Autowired
 private AopDao aopDao;
 
 public void method() {
  System.out.println("from AopUserService method");
  aopDao.method();
 }
}
```

:notes:`@Autowired`注解不需要属性具有`setXxx()`方法

### @Qualifier

```java
@Component(value = "aopUserService")
public class AopUserService {
 @Autowired
 @Qualifier(value = "aopDao")
 private AopDao aopDao;
 
 public void method() {
  System.out.println("from AopUserService method");
  aopDao.method();
 }
}
```

:notes:根据名称( id )注入，需要和`@Autowired`一起使用

### @Resource

```java
@Component(value = "aopUserService")
public class AopUserService {
 @Resource(name = "aopDao")
 private AopDao aopDao;
 
 public void method() {
  System.out.println("from AopUserService method");
  aopDao.method();
 }
}
```

:notes:`@Resource`可以根据类型注入，也可以根据名称注入

- `@Resource`：根据类型进行注入
- `@Resource(name = "aopDao")`：根据名称进行注入
- 此注解所在的包是`javax.annotation.Resource`

### @Value

```java
@Component(value = "aopUserService")
public class AopUserService {
    
 @Resource(name = "aopDao")
 private AopDao aopDao;
    
 @Value("pineapple-man")
 private String name;
 
 public void method() {
  System.out.println("from AopUserService method");
  aopDao.method();
  System.out.println(name);
 }
}
```

:notes:对普通类型属性进行注入

## 完全注解开发

不再需要冗长的配置文件，使用一个配置类代替所有的配置文件

### 注解配置类

```java
@Configuration
@ComponentScan(basePackages = {"com.pineapple_man.spring.aop"})
public class SpringConfig {
}
```

:sparkles:注解类特点

- `@Configuration`配置类注解，声明当前类是配置类
- `@ComponentScan(basePackages = {"com"})`自动扫描包注解

### 测试方法

```java
public void annotationConfigTest() {
 ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
 AopUserService aopUserService = applicationContext.getBean("aopUserService", AopUserService.class);
  aopUserService.method();
}
```
