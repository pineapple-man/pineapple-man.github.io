---
title: 深入分析 enum
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: JDK
tags: Java
keywords:
- enum
- java
excerpt: 枚举的概念很早就知道了，但是一直感觉认识尚浅，决定在这里对枚举进行总结
---
<!-- toc -->

## 枚举常见用法

JDK 1.5 之前并没有对枚举类的支持，在此之前，如果想要使用常量只能通过`public static final xxx`进行声明，如此声明并不是不行，但是当拥有大量常量声明使用时，将额外提升复杂度

JDK1.5 以后拥有了枚举类，可以将相关的常量分组到一个枚举类型里，每个枚举提供了比常量更多的方法

### 将枚举类型作为常量使用

```java
public enum Color {  
  RED, GREEN, BLANK, YELLOW  
} 
```

### switch

JDK1.6之前的switch语句只支持int,char,enum类型，使用枚举，能让我们的代码可读性更强

```java
enum Signal {  
    GREEN, YELLOW, RED  
}  
public class TrafficLight {  
    Signal color = Signal.RED;  
    public void change() {  
        switch (color) {  
        case RED:  
            color = Signal.GREEN;  
            break;  
        case YELLOW:  
            color = Signal.RED;  
            break;  
        case GREEN:  
            color = Signal.YELLOW;  
            break;  
        }  
    }  
}  
```

### 常用方法

enum 定义的枚举类默认继承了 java.lang.Enum 类，并实现了 java.lang.Seriablizable 和 java.lang.Comparable 两个接口

values(), ordinal() 和 valueOf() 方法位于 java.lang.Enum 类中

|    方法     |                     含义                     |
| :---------: | :------------------------------------------: |
| `values()`  |             返回枚举类中所有的值             |
| `ordinal()` | 可以找到每个枚举常量的索引，就像数组索引一样 |
| `valueOf()` |          返回指定字符串值的枚举常量          |

```java
enum Color
{
    RED, GREEN, BLUE;
}
 
public class Test
{
    public static void main(String[] args)
    {
        // 调用 values()
        Color[] arr = Color.values();
 
        // 迭代枚举
        for (Color col : arr)
        {
            // 查看索引
            System.out.println(col + " at index " + col.ordinal());
        }
        // 使用 valueOf() 返回枚举常量，不存在的会报错 IllegalArgumentException
        System.out.println(Color.valueOf("RED"));
    }
}
/**
RED at index 0
GREEN at index 1
BLUE at index 2
RED
**/
```

###  枚举类成员

枚举跟普通类一样可以用自己的变量、方法和构造函数，构造函数只能使用 private 访问修饰符，所以外部无法调用

枚举既可以包含具体方法，也可以包含抽象方法。 如果枚举类具有抽象方法，则枚举类的每个实例都必须实现它

```java
enum Color
{
    RED, GREEN, BLUE;
 
    // 构造函数
    private Color()
    {
        System.out.println("Constructor called for : " + this.toString());
    }
 
    public void colorInfo()
    {
        System.out.println("Universal Color");
    }
}
 
public class Test
{    
    // 输出
    public static void main(String[] args)
    {
        Color c1 = Color.RED;
        System.out.println(c1);
        c1.colorInfo();
    }
}
/**
Constructor called for : RED
Constructor called for : GREEN
Constructor called for : BLUE
RED
Universal Color
**/
```



## 1.5 枚举概述

:thinking:什么是枚举类型

- 一个只包含仅有几个可选项的变量类型，称之为枚举类型

:notes:Java原生并不支持枚举类型，但是可以通过类自定义实现枚举类型

:sparkles:枚举类型，是一种与单例模式不同的**多例模式**，枚举类型就是一个多例类，拥有有限的实例数

### 自定义实现枚举

```java
class Myenum {
  public static final Myenum FRONT = new Myenum("前");//确定当前类需要包含的特定值
  private String name;

  private Myenum() {}

  private Myenum(String name) {
    this.name = name;
  }

  public String getName() {
    return name;
  }
}
public static void main(String[] args) {
    Myenum myenum = Myenum.FRONT;
    System.out.println(myenum);
    System.out.println(myenum.getName());
}//可以通过 "类.成员"方式获得枚举类型中的例子
```

方式二:

```java
abstract class Myenum {
  public static final Myenum FRONT =	//抽象类的赋值
      new Myenum("前") {
        @Override
        public void show() {
          System.out.println(this.getName());
        }
      };
  private String name;

  public Myenum() {}

  public Myenum(String name) {
    this.name = name;
  }

  public String getName() {
    return this.name;
  }

  public abstract void show();
}
public static void main(String[] args) {
    Myenum myenum = Myenum.FRONT;
    System.out.println(myenum);
    System.out.println(myenum.getName());
}
```

### 系统内置枚举

系统内置的枚举(enum)是一个和类相同的定义，单独的放在一个文件之中

```java
public enum MYENUM {
  FRONT("前"),
  BEHIND("后"),
  LEFT("左"),
  RIGHT("右");//最后一个私有成员变量必须有分号

  private MYENUM() {}

  private String name;

  private MYENUM(String name) {
    this.name = name;
  }

  public void getName() {
    System.out.println(this.name);
  }
}
public static void main(String[] args) {
    System.out.println(MYENUM.FRONT); // 重写了toString方法,输出为当前私有变量的名字 front
    MYENUM.FRONT.getName();
}
```

如果是具有抽象类的枚举类型

```java
public enum MYENUM {
  FRONT("前") {
    @Override
    public void show() {
      super.getName();
    }
  };

  private MYENUM() {}

  private String name;

  private MYENUM(String name) {
    this.name = name;
  }

  public void getName() {
    System.out.println(this.name);
  }

  public abstract void show();
}
public static void main(String[] args) {
    //访问方法一致
    System.out.println(MYENUM.FRONT); 
    MYENUM.FRONT.getName();
}
```

### 枚举在switch中的使用

```java
public static void main(String[] args) {
    switch (MYENUM.FRONT) {
      case FRONT:
        System.out.println("You choose the option of front");
        break;
      default:
        System.out.println("没有或者选项");
        break;
	}
}
```

### 注意事项

1. 定义枚举类要用关键字`enum`
2. 所有枚举类都是enum的子类
3. 枚举类的第一行必须是枚举项，最后一个枚举项后的分号是可以省略的，但是如果枚举类有其他的东西，这个分号就不能省略，建议不要省略
4. 枚举类也可以有构造器，但必须是private修饰的,它默认的也是private的，枚举项构造用法比较特殊:枚举项("")
5. 枚举类也可以有构造方法，但是枚举项必须重写该方法
6. 枚举在switch语句中的使用

[Java 枚举(enum) 详解7种常见的用法](https://blog.csdn.net/qq_27093465/article/details/52180865)





## 附录

[Java 枚举(enum)](https://www.runoob.com/java/java-enum.html)



https://www.liaoxuefeng.com/wiki/1252599548343744/1260473188087424

https://blog.csdn.net/javazejian/article/details/71333103