---
title: AOP——面向切面编程
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: Java
tags: Spring
keywords: 
    -AOP
excerpt: 本文主要讲解Spring中另一重要的概念“AOP”，那么到底什么是AOP？为什么要使用AOP？
---
## AoP 底层原理

:notes:AoP 的​底层原理——**动态代理**
:sparkles:动态代理分类
- 有接口情况，使用 JDK 动态代理
- 没有接口情况，使用 CGLIB 动态代理

### JDK 动态代理

创建接口实现类代理对象，增强类的方法

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210608131656646.png %}
#### 1.1.1 创建测试接口类

```java
public interface Dao {
   public void add(int a, int b);
   public String update(String id);
}
```

```java
public class UserDaoImpl implements Dao {
   @Override
   public void add(int a, int b) {
      System.out.println(a + b);
      System.out.println("from userdao implementes");
   }
   
   @Override
   public String update(String id) {
      System.out.println(id);
      System.out.println("from userdao implements update method");
      return id;
   }
}
```

#### 1.1.2 创建接口代理类

```java
public class UserDaoProxy implements InvocationHandler {
   private Object object;
   
   public UserDaoProxy() {
   }
   
   public UserDaoProxy(Object object) {
      this.object = object;
   }
   
    //增强方法
   @Override
   public Object invoke(Object proxy, @NotNull Method method, Object[] args) throws Throwable {
      System.out.println("invoke before");
      System.out.println(method.getName());
      System.out.println(Arrays.toString(args));
      Object invoke = method.invoke(object, args);
      System.out.println("after");
      System.out.println(object);
      return invoke;
   }
}
```

### 1.3 方法增强

```java
public class JDKProxy {
   public static void main(String[] args) {
      Class[] intefaces = {Dao.class};
      UserDaoImpl userDao = new UserDaoImpl();
      Dao dao = (Dao) Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), intefaces, new UserDaoProxy(userDao));
       //增强后的方法使用
      dao.update("123");
      dao.add(1, 2);
   }
}
```

### 1.2 CGLIB 动态代理

创建子类的代理对象，增强类的方法

{% image fancybox fig-100  center https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210608131659303.png %}

## 2. AoP 术语

> - 连接点：类中可以被增强的方法
> - 切入点：被增强的方法
> - 通知（增强）
>   1. 对原方法增加的逻辑部分称为通知（增强）
>   2. 通知有多种类型：前置通知、后置通知、环绕通知、异常通知、最终通知
> - 切面：把通知应用到切入点过程

## 3. AoP 操作

Spring 使用 AspectJ 进行 AoP 操作，与 IoC 相同，AoP 分为：

- 基于 XML 配置文件实现
- 基于注解方式实现

### 3.1 导包

:books:需要导入额外的包

- `com.springsource.net.sf.cglib-2.2.0.jar`
- `com.springsource.org.aopalliance-1.0.0.jar`
- `com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar`

### 3.2 切入点表达式

切入点表达式作用：明确对哪个类中的哪个方法进行进行增强

格式：`execution([权限修饰符][返回类型][类全路径][方法名称][参数列表])`

:notes:切入点表达式注意点

- 如果参数拥有多个，使用`..`表示
- 使用`*`表示任意类型、方法的通配

## 4. AspectJ 注解

原本业务逻辑类

```java
@Component
public class User {
   public void add(){
      System.out.println("from User's add method");
   }
}
```

### 4.1 创建增强类

```java
@Component
public class AdvanceUser {
   public void before() {
      System.out.println("preprocess from AdvanceUser's before method");
   }
}
```

### 4.2 Spring 配置文件打开注解扫描

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <context:component-scan base-package="com.pineapple_man.spring.aop"></context:component-scan>
</beans>
```

### 4.3 声明增强代理类

```java
@Aspect
@Component
public class AdvanceUser {
	public void before() {
		System.out.println("preprocess from AdvanceUser's before method");
	}
}
```

### 4.4 Spring 配置文件生成代理对象

增加 aop 名称空间

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <context:component-scan base-package="com.pineapple_man.spring.aop"></context:component-scan>
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```

### 4.5 配置不同类型的通知

#### 4.5.1 前置通知

:notes:调用方法之前执行

```java
@Before(value = "execution(* com.pineapple_man.spring.aop.aspect.User.add(..))")
public void before() {
	System.out.println("preprocess from AdvanceUser's before method");
}
```

#### 4.5.2 后置通知

:notes:方法正常返回后执行

```java
@AfterReturning(value = "execution(* com.pineapple_man.spring.aop.aspect.User.add(..))")
public void afterReturning() {
   System.out.println("after returning process from AdvanceUser's afterReturning method");
}
```

#### 4.5.3 最终通知

:notes:方法不论执行成功与否，均在调用方法之后执行

```java
@After(value = "execution(* com.pineapple_man.spring.aop.aspect.User.add(..))")
public void after() {
	System.out.println("after process from AdvanceUser's after method");
}
```

#### 4.5.4 异常通知

:notes:只有原方法出现异常时，才会增强；并且是作为最后方法被执行的

```java
@AfterThrowing(value = "execution(* com.pineapple_man.spring.aop.aspect.User.add(..))")
public void afterThrowing() {
	System.out.println("external throw process method from AdvanceUser's");
}
```

#### 4.5.5 环绕通知

:notes:手动在方法前后增加通知

```java
@Around(value = "execution(* com.pineapple_man.spring.aop.aspect.User.add(..))")
public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
	System.out.println("方法执行前处理");
	proceedingJoinPoint.proceed();
	System.out.println("方法执行后处理");
}
```

### 4.6 增强后执行顺序

1. `@Around`——方法前处理
2. `@Before`
3. 原方法
4. `@Around`——方法后处理
5. `@After`
6. `@AfterReturning`（如果出现异常改为执行`@AfterThrowing`）

```
方法执行前处理
preprocess from AdvanceUser's before method
from User's add method
方法执行后处理
after process from AdvanceUser's after method
after returning process from AdvanceUser's afterReturning method
```

### 4.7 增强类优先级

如果存在，多个类对同一个方法进行增强，需要**设置增强类优先级**

:sparkles:`@Order(数字值)`注解可以设置增强类的优先级；**数字越小，优先级越高**

```java
@Component
@Aspect
@Order(1)
public class PersonProxy{}
```

### 4.8 完全注解开发

只需要创建配置类，不需要创建 XML 配置文件

```java
@Configuration
@ComponentScan(basePackages = {"com.pineapple_man.spring.aop.aspect"})
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class AopConfig {
}
```

测试类

```java
public void dynamicProxyBaseAnnotation() {
   ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AopConfig.class);
   User user = applicationContext.getBean("user", User.class);
   user.add();
}
```

## 5. AspectJ XML 配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">
    <bean class="com.pineapple_man.spring.aop.aspect.User" id="user"></bean>
    <bean class="com.pineapple_man.spring.aop.aspect.AdvanceUser" id="advanceUser"></bean>
    <aop:config>
        <aop:pointcut id="p" expression="execution(* com.pineapple_man.spring.aop.aspect.User.add(..))"/>
        <aop:aspect ref="advanceUser">
            <aop:before method="before" pointcut-ref="p"></aop:before>
        </aop:aspect>
    </aop:config>
</beans>
```

