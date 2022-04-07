---
title: 深入分析 Java 枚举
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: 
- Java
tags: JDK
keywords:
- enum
excerpt: 枚举的概念很早就知道了，但是一直感觉认识尚浅，决定在这里对枚举进行总结
---

## 概述



### 为什么需要枚举

在Java中，我们可以通过`static final`来定义常量。例如，我们希望定义周一到周日这7个常量，可以用7个不同的`int`表示：

```java
public class Weekday {
    public static final int SUN = 0;
    public static final int MON = 1;
    public static final int TUE = 2;
    public static final int WED = 3;
    public static final int THU = 4;
    public static final int FRI = 5;
    public static final int SAT = 6;
}
```

使用常量的时候，可以这么引用：

```java
if (day == Weekday.SAT || day == Weekday.SUN) {
    // TODO: work at home
}
```

也可以把常量定义为字符串类型，例如，定义3种颜色的常量：

```java
public class Color {
    public static final String RED = "r";
    public static final String GREEN = "g";
    public static final String BLUE = "b";
}
```

使用常量的时候，可以这么引用：

```
String color = ...
if (Color.RED.equals(color)) {
    // TODO:
}
```

无论是`int`常量还是`String`常量，使用这些常量来表示一组枚举值的时候，有一个严重的问题就是，**编译器无法检查每个值的合理性**。例如：

```java
if (weekday == 6 || weekday == 7) {
    if (tasks == Weekday.MON) {
        // TODO:
    }
}
```

上述代码编译和运行均不会报错，但存在两个问题：

- 注意到`Weekday`定义的常量范围是`0`~`6`，并不包含`7`，编译器无法检查不在枚举中的`int`值；
- 定义的常量仍可与其他变量比较，但其用途并非是枚举星期值

为了让编译器能自动检查某个值在枚举的集合内，并且，不同用途的枚举需要不同的类型来标记，不能混用，JDK1.5 引入一种特殊的数据类型：**枚举类型**

之所以特殊是因为它即是一种Class类型，同时比其他的类类型多了些特殊的约束，但是这些约束的存在也就造就了枚举类型的简洁性、安全性以及便捷性

枚举类型并不是基本数据类型，因为此时java已经设计完成，不可能增加新的基础数据类型，只能通过面向对象的思想拓展出新的类型【不得不佩服面向对象思想的强大，Java里面处处是对象YYDS】，



```java
public class Main {
    public static void main(String[] args) {
        Weekday day = Weekday.SUN;
        if (day == Weekday.SAT || day == Weekday.SUN) {
            System.out.println("Work at home!");
        } else {
            System.out.println("Work at office!");
        }
    }
}
/*
值一般是大写的字母，多个值之间以逗号分隔
*/
enum Weekday {
    SUN, MON, TUE, WED, THU, FRI, SAT;
}
```

注意到定义枚举类是通过关键字`enum`实现的，我们只需依次列出枚举的常量名。

和`int`定义的常量相比，使用`enum`定义枚举有如下好处：

首先，`enum`常量本身带有类型信息，即`Weekday.SUN`类型是`Weekday`，编译器会自动检查出类型错误。例如，下面的语句不可能编译通过：

```java
int day = 1;
if (day == Weekday.SUN) { // Compile error: bad operand types for binary operator '=='
}
```

最后，不同类型的枚举不能互相比较或者赋值，因为类型不符。例如，不能给一个`Weekday`枚举类型的变量赋值为`Color`枚举类型的值：

```java
Weekday x = Weekday.SUN; // ok!
Weekday y = Color.RED; // Compile error: incompatible types
```

这就使得编译器可以在编译期自动检查出所有可能的潜在错误

### enum的比较

使用`enum`定义的枚举类是一种引用类型。前面我们讲到，引用类型比较，要使用`equals()`方法，如果使用`==`比较，它比较的是两个引用类型的变量是否是同一个对象。因此，引用类型比较，要始终使用`equals()`方法，但`enum`类型可以例外。

这是因为`enum`类型的每个常量在JVM中只有一个唯一实例，所以可以直接用`==`比较

```java
if (day == Weekday.FRI) { // ok!
}
if (day.equals(Weekday.SUN)) { // ok, but more code!
}
```

### enum类型

通过`enum`定义的枚举类，和其他的`class`有什么区别？

答案是没有任何区别。`enum`定义的类型就是`class`，只不过它有以下几个特点：

- 定义的`enum`类型总是继承自`java.lang.Enum`，且无法被继承；
- 只能定义出`enum`的实例，而无法通过`new`操作符创建`enum`的实例；
- 定义的每个实例都是引用类型的唯一实例；
- 可以将`enum`类型用于`switch`语句。

例如，我们定义的`Color`枚举类：

```
public enum Color {
    RED, GREEN, BLUE;
}
```

编译器编译出的`class`大概就像这样：

```java
public final class Color extends Enum { // 继承自Enum，标记为final class
    // 每个实例均为全局唯一:
    public static final Color RED = new Color();
    public static final Color GREEN = new Color();
    public static final Color BLUE = new Color();
    // private构造方法，确保外部无法调用new操作符:
    private Color() {}
}
```

所以，编译后的`enum`类和普通`class`并没有任何区别。但是我们自己无法按定义普通`class`那样来定义`enum`，必须使用`enum`关键字，这是Java语法规定的。

因为`enum`是一个`class`，每个枚举的值都是`class`实例，因此，这些实例有一些方法：

|   方法    |               含义                |                   举例                    |
| :-------: | :-------------------------------: | :---------------------------------------: |
|  name()   |            返回常量名             | `String s = Weekday.SUN.name(); // "SUN"` |
| ordinal() | 返回定义的常量的顺序，从0开始计数 |   `int n = Weekday.MON.ordinal(); // 1`   |
|           |                                   |                                           |



:notes:改变枚举常量定义的顺序就会导致`ordinal()`返回值发生变化

```java
enum Weekday1 {
    SUN, MON, TUE, WED, THU, FRI, SAT;
}
enum Weekday2 {
    MON, TUE, WED, THU, FRI, SAT, SUN;
}
/**这两个类的oridinal就是不同的，如果在代码中编写了类似if(x.ordinal()==1)这样的语句，就要保证enum的枚举顺序不能变。新增的常量必须放在最后
**/
```

有些童鞋会想，`Weekday`的枚举常量如果要和`int`转换，使用`ordinal()`不是非常方便？比如这样写：

```java
String task = Weekday.MON.ordinal() + "/ppt";
saveToFile(task);
```

但是，如果不小心修改了枚举的顺序，编译器是无法检查出这种逻辑错误的。要编写健壮的代码，就不要依靠`ordinal()`的返回值。因为`enum`本身是`class`，所以我们可以定义`private`的构造方法，并且，给每个枚举常量添加字段：

```java
public class Main {
    public static void main(String[] args) {
        Weekday day = Weekday.SUN;
        if (day.dayValue == 6 || day.dayValue == 0) {
            System.out.println("Work at home!");
        } else {
            System.out.println("Work at office!");
        }
    }
}

enum Weekday {
    MON(1), TUE(2), WED(3), THU(4), FRI(5), SAT(6), SUN(0);

    public final int dayValue;

    private Weekday(int dayValue) {
        this.dayValue = dayValue;
    }
}
```

这样就无需担心顺序的变化，新增枚举常量时，也需要指定一个`int`值。



 注意：枚举类的字段也可以是非final类型，即可以在运行期修改，但是不推荐这样做！

默认情况下，对枚举常量调用`toString()`会返回和`name()`一样的字符串。但是，`toString()`可以被覆写，而`name()`则不行。我们可以给`Weekday`添加`toString()`方法：

```java
public class Main {
    public static void main(String[] args) {
        Weekday day = Weekday.SUN;
        if (day.dayValue == 6 || day.dayValue == 0) {
            System.out.println("Today is " + day + ". Work at home!");
        } else {
            System.out.println("Today is " + day + ". Work at office!");
        }
    }
}

enum Weekday {
    MON(1, "星期一"), TUE(2, "星期二"), WED(3, "星期三"), THU(4, "星期四"), FRI(5, "星期五"), SAT(6, "星期六"), SUN(0, "星期日");

    public final int dayValue;
    private final String chinese;

    private Weekday(int dayValue, String chinese) {
        this.dayValue = dayValue;
        this.chinese = chinese;
    }

    @Override
    public String toString() {
        return this.chinese;
    }
}
```

覆写`toString()`的目的是在输出时更有可读性

注意：判断枚举常量的名字，要始终使用name()方法，绝不能调用toString()！

### switch

最后，枚举类可以应用在`switch`语句中。因为枚举类天生具有类型信息和有限个枚举常量，所以比`int`、`String`类型更适合用在`switch`语句中：

```java
public class Main {
    public static void main(String[] args) {
        Weekday day = Weekday.SUN;
        switch(day) {
        case MON:
        case TUE:
        case WED:
        case THU:
        case FRI:
            System.out.println("Today is " + day + ". Work at office!");
            break;
        case SAT:
        case SUN:
            System.out.println("Today is " + day + ". Work at home!");
            break;
        default:
            throw new RuntimeException("cannot process " + day);
        }
    }
}

enum Weekday {
    MON, TUE, WED, THU, FRI, SAT, SUN;
}
```

加上`default`语句，可以在漏写某个枚举常量时自动报错，从而及时发现错误





### 常用方法

enum 定义的枚举类默认继承了 java.lang.Enum 类，并实现了 java.lang.Seriablizable 和 java.lang.Comparable 两个接口

values(), ordinal() 和 valueOf() 方法位于 java.lang.Enum 类中

|    方法     |                     含义                     |
| :---------: | :------------------------------------------: |
| `values()`  |             返回枚举类中所有的值             |
| `ordinal()` | 可以找到每个枚举常量的索引，就像数组索引一样 |
| `valueOf()` |          返回指定字符串值的枚举常量          |



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



## 枚举实现原理

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

实际上在使用关键字`enum`创建枚举类型并编译后，编译器会为我们生成一个相关的类，这个类继承了Java API中的`java.lang.Enum`类，也就是说通过关键字`enum`创建枚举类型在编译后事实上也是一个类类型

```java
//反编译Day.class
final class Day extends Enum
{
    //编译器为我们添加的静态的values()方法
    public static Day[] values()
    {
        return (Day[])$VALUES.clone();
    }
    //编译器为我们添加的静态的valueOf()方法，注意间接调用了Enum也类的valueOf方法
    public static Day valueOf(String s)
    {
        return (Day)Enum.valueOf(com/pineapple/enumdemo/Day, s);
    }
    //私有构造函数
    private Day(String s, int i)
    {
        super(s, i);
    }
     //前面定义的7种枚举实例
    public static final Day MONDAY;
    public static final Day TUESDAY;
    public static final Day WEDNESDAY;
    public static final Day THURSDAY;
    public static final Day FRIDAY;
    public static final Day SATURDAY;
    public static final Day SUNDAY;
    private static final Day $VALUES[];

    static 
    {    
        //实例化枚举实例
        MONDAY = new Day("MONDAY", 0);
        TUESDAY = new Day("TUESDAY", 1);
        WEDNESDAY = new Day("WEDNESDAY", 2);
        THURSDAY = new Day("THURSDAY", 3);
        FRIDAY = new Day("FRIDAY", 4);
        SATURDAY = new Day("SATURDAY", 5);
        SUNDAY = new Day("SUNDAY", 6);
        $VALUES = (new Day[] {
            MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
        });
    }
}
```

从反编译的代码可以发现，编译器的确将枚举类型编程了类类型（该类是final类型的，将无法被继承）

系统内置的枚举(enum)是一个和类相同的定义，单独的放在一个文件之中该类是一个抽象类(稍后我们会分析该类中的主要方法)，除此之外，编译器还帮助我们生成了7个Day类型的实例对象分别对应枚举中定义的7个日期，这也充分说明了我们前面使用关键字enum定义的Day类型中的每种日期枚举常量也是实实在在的Day实例对象，只不过代表的内容不一样而已。注意编译器还为我们生成了两个静态方法，分别是values()和 valueOf()

使用关键字enum定义的枚举类型，在编译期后，也将转换成为一个实实在在的类，而在该类中，会存在每个在枚举类型中定义好变量的对应实例对象，如上述的MONDAY枚举类型对应public static final Day MONDAY;，同时编译器会为该类创建两个方法，分别是values()和valueOf()


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



### 注意事项

1. 定义枚举类要用关键字`enum`
2. 所有枚举类都是enum的子类
3. 枚举类的第一行必须是枚举项，最后一个枚举项后的分号是可以省略的，但是如果枚举类有其他的东西，这个分号就不能省略，建议不要省略
4. 枚举类也可以有构造器，但必须是private修饰的,它默认的也是private的，枚举项构造用法比较特殊:枚举项("")
5. 枚举类也可以有构造方法，但是枚举项必须重写该方法
6. 枚举在switch语句中的使用

## 枚举类常用方法

### Enum抽象类常见方法

Enum是所有 Java 语言枚举类型的公共基本类（注意Enum是抽象类），以下是它的常见方法：

返回类型	方法名称	方法说明
		
	
		
	
	
	

		。

|           返回类型            |                      方法名                      |                             含义                             |
| :---------------------------: | :----------------------------------------------: | :----------------------------------------------------------: |
|             `int`             |                 `compareTo(E o)`                 |                  比较此枚举与指定对象的顺序                  |
|         `boolean	`         |              `equals(Object other)`              |            当指定对象等于此枚举常量时，返回 true             |
|          `Class<?>`           |              `getDeclaringClass()`               |        返回与此枚举常量的枚举类型相对应的 Class 对象         |
|           `String`            |                     `name()`                     |       返回此枚举常量的名称，在其枚举声明中对其进行声明       |
|           `int	`           |                   `ordinal()`                    | 返回枚举常量的序数（它在枚举声明中的位置，其中初始常量序数为零） |
|           `String`            |                   `toString()`                   |        返回枚举常量的名称，它包含在声明中(可以被覆盖)        |
| `static<T extends Enum<T>> T` | `static valueOf(Class<T> enumType, String name)` |            返回带指定名称的指定枚举类型的枚举常量            |





## 附录

[Java 枚举(enum)](https://www.runoob.com/java/java-enum.html)

[廖雪峰](https://www.liaoxuefeng.com/wiki/1252599548343744/1260473188087424)

[深入理解Java枚举类型(enum)](https://blog.csdn.net/javazejian/article/details/71333103)

[我怀疑你没怎么用过枚举](https://segmentfault.com/a/1190000022040425)