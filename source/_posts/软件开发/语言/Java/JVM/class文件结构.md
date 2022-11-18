title: class 文件结构
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-04-03 22:51:21
thumbnailImage:
categories: Java
tags: JVM
keywords: class
excerpt: 本文主要讲述 java 中字节码文件的格式

---

<!-- toc -->

## 概述

:question: 为什么 JVM 是平台无关的？
{% alert success no-icon %}

- 平台无关性：任何操作系统都能运行 Java 代码
- 语言无关性： JVM 能运行除 Java 以外的其他代码

{% endalert %}

Java 源代码首先需要使用 `Javac` 编译器编译成 `.class` 文件，然后由 JVM 执行 `.class` 文件，从而程序开始执行。 JVM 只认识 `.class` 文件，他不关心是何种语言生成了 `.class` 文件，只要 `.class` 文件符合 JVM 的规范就能运行。

目前 JRuby、Jython、Scala 等语言能够在 JVM 上运行。它们有各自的语法规则，不过它们的编译器都能将各自的源码编译成符合 JVM 规范的 .class 文件，从而能够借助 JVM 运行它们。

Java 语言中的各种变量、关键字和运算符号的语义最终都是由多条字节码命令组合而成的，因此字节码命令所能提供的语义描述能力肯定会比 Java 语言本身更加强大。因此，有一些 Java 语言本身无法有效支持的语言特性，不代表字节码本身无法有效支持。

:sparkles: 字节码文件的跨平台性

1. **Java 语言，是一种跨平台的语言**（`write once,run anywhere`)
2. 当 Java 源代码成功编译成字节码后，如果想在不同的平台上面运行，则无须再次编译
3. 跨平台已经成为趋势，Java 已经不再是唯一的跨平台语言
4. **Java 虚拟机：跨语言的平台**

## Class 文件

任何一个 Class 文件都对应着唯一一个类或接口的定义信息，但反过来说，Class 文件实际上它并不一定以磁盘文件形式存在。Class 文件是一组以字节为基础单位的**二进制流**

### 什么是 class 文件

{% alert info no-icon %}

Java 字节码类文件（.class）是 Java 编译器编译 Java 源文件（.java）产生的「 目标文件 」。它是一种二进制流文件，各个数据项按顺序紧密的从前向后排列，相邻的项之间没有间隙， 这样可以使得 class 文件非常紧凑， 体积轻巧， 可以被 JVM 快速的加载至内存， 并且占据较少的内存空间（方便于网络的传输）。

{% endalert %}

Java 源文件在被 Java 编译器编译之后， 每个类（或者接口）都单独占据一个 class 文件， 并且类中的所有信息都会在 class 文件中有相应的描述， 由于 class 文件很灵活， 它甚至比 Java 源文件有着更强的描述能力

### class 文件结构

Class 的结构不像 XML 等描述语言，由于它没有任何分隔符号。所以在其中的数据项，无论是字节顺序还是数量，都是被严格限定的，哪个字节代表什么含义，长度是多少，先后顺序如何，都不允许改变。Class 文件是二进制文件，它的内容具有严格的规范，文件中没有任何空格，全都是连续的 0/1。

:sparkles:Class 文件格式采用一种类似于 C 语言结构体的方式进行数据存储，这种结构中只有两种数据类型：**无符号数**和**表**

<style type="text/css">
.tg  {border:none;border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-3czb{background-color:#409CFF;border-color:inherit;color:#FFF;font-family:"Times New Roman", Times, serif !important;
  font-size:medium;position:-webkit-sticky;position:sticky;text-align:center;top:-1px;vertical-align:middle;
  will-change:transform}
.tg .tg-d8pr{background-color:#409CFF;color:#FFF;font-size:medium;position:-webkit-sticky;position:sticky;text-align:center;
  top:-1px;vertical-align:middle;will-change:transform}
.tg .tg-ivxw{background-color:#EBF5FF;color:#444;font-size:medium;text-align:center;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-3czb"><span style="font-weight:400;color:#FFF;background-color:#409CFF">数据类型</span></th>
    <th class="tg-d8pr"><span style="font-weight:400;color:#FFF;background-color:#409CFF">定义</span></th>
    <th class="tg-d8pr"><span style="font-weight:400;color:#FFF;background-color:#409CFF">说明</span></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-ivxw"><span style="color:#444;background-color:#EBF5FF">无符号数</span></td>
    <td class="tg-ivxw"><span style="color:#444;background-color:#EBF5FF">无符号数用来描述数字、索引引用、数量值或按照utf-8编码构成的字符串值</span></td>
    <td class="tg-ivxw"><span style="color:#444;background-color:#EBF5FF">其中无符号数属于基本的数据类型。</span><br><span style="color:#444;background-color:#EBF5FF">u1、u2、u4、u8来分别代表1个字节、2个字节、4个字节和8个字节</span></td>
  </tr>
  <tr>
    <td class="tg-ivxw"><span style="color:#444;background-color:#EBF5FF">表</span></td>
    <td class="tg-ivxw"><span style="color:#444;background-color:#EBF5FF">表是由多个无符号数或其他表构成的复合数据结构</span></td>
    <td class="tg-ivxw"><span style="color:#444;background-color:#EBF5FF">所有的表都以“_info”结尾。</span><br><span style="color:#444;background-color:#EBF5FF">由于表没有固定长度，所以通常会在其前面加上长度申明。</span><br><span style="color:#444;background-color:#EBF5FF">表的长度申明称之为表头，表的数据部分称之为表项数据</span></td>
  </tr>
</tbody>
</table>

### class 文件结构概述

Class 文件的结构并不是一成不变的，随着 Java 虚拟机的不断发展，总是不可避免地会对 Class 文件结构做出一些调整，但是其基本结构和框架是非常稳定的

{% alert success no-icon %}

Class 文件具体由以下几个构成：魔数、版本信息、常量池、访问标志、类索引、父类索引、接口索引集合、字段表集合、方法表集合、属性表集合

{% endalert %}

各个部分的具体组成如下：

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:1px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:1px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-uuga{background-color:#409CFF;border-color:#343434;color:#ffffff;font-family:"Times New Roman", Times, serif !important;
  font-size:medium;position:-webkit-sticky;position:sticky;text-align:center;top:-1px;vertical-align:middle;
  will-change:transform}
.tg .tg-zs1u{background-color:#409CFF;border-color:#343434;color:#FFF;font-family:"Times New Roman", Times, serif !important;
  font-size:medium;position:-webkit-sticky;position:sticky;text-align:center;top:-1px;vertical-align:middle;
  will-change:transform}
.tg .tg-7rkz{background-color:#EBF5FF;border-color:#343434;color:#444;font-family:"Times New Roman", Times, serif !important;
  font-size:medium;text-align:center;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-zs1u"></th>
    <th class="tg-zs1u"><span style="font-weight:400;color:#FFF;background-color:#409CFF">类型</span></th>
    <th class="tg-zs1u"><span style="font-weight:400;color:#FFF;background-color:#409CFF">名称</span></th>
    <th class="tg-zs1u"><span style="font-weight:400;color:#FFF;background-color:#409CFF">说明</span></th>
    <th class="tg-zs1u"><span style="font-weight:400;color:#FFF;background-color:#409CFF">长度</span></th>
    <th class="tg-uuga"><span style="font-weight:400;background-color:#409CFF">数量</span></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">魔数</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u4</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">magic</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">魔数,识别Class文件格式</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">4个字节</span></td>
    <td class="tg-7rkz">1</td>
  </tr>
  <tr>
    <td class="tg-7rkz" rowspan="2"><span style="color:#444;background-color:#EBF5FF">版本号</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">minor_version</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">副版本号(小版本)</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">major_version</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">主版本号(大版本)</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz" rowspan="2"><span style="color:#444;background-color:#EBF5FF">常量池集合</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">constant_pool_count</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">常量池计数器</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">cp_info</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">constant_pool</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">常量池表</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">n个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">constant_pool_count - 1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">访问标识</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">access_flags</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">访问标识</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz" rowspan="4"><span style="color:#444;background-color:#EBF5FF">索引集合</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">this_class</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">类索引</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">super_class</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">父类索引</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">interfaces_count</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">接口计数器</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">interfaces</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">接口索引集合</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">interfaces_count</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz" rowspan="2"><span style="color:#444;background-color:#EBF5FF">字段表集合</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">fields_count</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">字段计数器</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">field_info</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">fields</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">字段表</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">n个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">fields_count</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz" rowspan="2"><span style="color:#444;background-color:#EBF5FF">方法表集合</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">methods_count</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">方法计数器</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">method_info</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">methods</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">方法表</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">n个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">methods_count</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz" rowspan="2"><span style="color:#444;background-color:#EBF5FF">属性表集合</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">u2</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">attributes_count</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">属性计数器</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">2个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">1</span></td>
  </tr>
  <tr>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">attribute_info</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">attributes</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">属性表</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">n个字节</span></td>
    <td class="tg-7rkz"><span style="color:#444;background-color:#EBF5FF">attributes_count</span></td>
  </tr>
</tbody>
</table>

接下来将开各个部门的详细讲解

## 魔数：Class 文件的标志

魔数是 Class 文件的标识符

:sparkles: 什么是 Magic Number(魔数)

- 每个 Class 文件开头的 4 个字节的无符号整数称为魔数(Magic Number)
- 它的唯一作用是确定这个文件是否能被虚拟机接受为合法的 Class 文件
- 魔数值固定为 `0xCAFEBABE`，不会改变
- 如果一个 Class 文件不以 0xCAFEBABE 开头，虚拟机在进行文件校验的时候就会直接抛出错误
- 使用魔数而不是扩展名来进行识别主要是基于安全方面的考虑，因为文件扩展名可以随意地改动

如果一个 Class 文件的魔数出错，将触发下列错误：

Error: A JNI error has occurred, please check your installation and try again Exception in thread "main" java.lang.ClassFormatError: Incompatible magic value 1885430635 in class file StringTest

## 版本号

:sparkles:Class 文件版本号特点

- Class 文件的版本号存储在魔数后面的 4 个字节中
- 第 5 个和第 6 个字节所代表的含义就是编译的副版本号 minor_version
- 第 7 个和第 8 个字节就是编译的主版本号 major_version
- 主版本号与副版本号共同构成了 Class 文件的格式版本号
- Java 的版本号是从 45 开始的，JDK 1.1 之后的每个 JDK 大版本发布主版本号向上加 1，次版本只在 J2SE 1.2 之前用过，从 1.2 开始基本上就没什么用了（都是 0）
- **不同版本的 Java 编译器编译的 Class 文件对应的版本是不一样的。目前，高版本的 Java 虚拟机可以执行由低版本编译器生成的 Class 文件，但是低版本的 Java 虚拟机不能执行由高版本编译器生成的 Class 文件。否则 JVM 会抛出 `java.lang.UnsupportedClassVersionError` 异常(向下兼容)**
- 在实际应用中，由于开发环境和生产环境的不同，可能会导致版本不一致问题的发生。需要特别注意开发编译的 JDK 版本和生产环境的 JDK 版本是否一致

:book:譬如某个 Class 文件的主版本号为 M，服版本号为 m，那么这个 Class 文件的格式版本号就确定为 M.m。下表列出了版本号和 Java 编译器的对应关系如下表：

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-yxec{color:#333;text-align:center;vertical-align:middle}
.tg .tg-leit{color:#333;font-weight:bold;text-align:center;vertical-align:middle}
.tg .tg-hxm3{background-color:#F8F8F8;color:#333;text-align:center;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-leit">主版本<br>（十进制）</th>
    <th class="tg-leit">副版本<br>（十进制）</th>
    <th class="tg-leit">编译器版本</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-yxec">45</td>
    <td class="tg-yxec">3</td>
    <td class="tg-yxec">1.1</td>
  </tr>
  <tr>
    <td class="tg-hxm3">46</td>
    <td class="tg-hxm3">0</td>
    <td class="tg-hxm3">1.2</td>
  </tr>
  <tr>
    <td class="tg-yxec">47</td>
    <td class="tg-yxec">0</td>
    <td class="tg-yxec">1.3</td>
  </tr>
  <tr>
    <td class="tg-hxm3">48</td>
    <td class="tg-hxm3">0</td>
    <td class="tg-hxm3">1.4</td>
  </tr>
  <tr>
    <td class="tg-yxec">49</td>
    <td class="tg-yxec">0</td>
    <td class="tg-yxec">1.5</td>
  </tr>
  <tr>
    <td class="tg-hxm3">50</td>
    <td class="tg-hxm3">0</td>
    <td class="tg-hxm3">1.6</td>
  </tr>
  <tr>
    <td class="tg-yxec">51</td>
    <td class="tg-yxec">0</td>
    <td class="tg-yxec">1.7</td>
  </tr>
  <tr>
    <td class="tg-hxm3">52</td>
    <td class="tg-hxm3">0</td>
    <td class="tg-hxm3">1.8</td>
  </tr>
  <tr>
    <td class="tg-yxec">53</td>
    <td class="tg-yxec">0</td>
    <td class="tg-yxec">1.9</td>
  </tr>
  <tr>
    <td class="tg-hxm3">54</td>
    <td class="tg-hxm3">0</td>
    <td class="tg-hxm3">1.10</td>
  </tr>
  <tr>
    <td class="tg-yxec">55</td>
    <td class="tg-yxec">0</td>
    <td class="tg-yxec">1.11</td>
  </tr>
</tbody>
</table>

## 常量池集合

常量池是 Class 文件中内容最为丰富的区域之一。常量池对于 Class 文件中的字段和方法解析也有着至关重要的作用。

随着 Java 虚拟机的不断发展，常量池的内容也日渐丰富。可以说，常量池是整个 Class 文件的基石。

Java 虚拟机规范一共定义了 14 种常量

常量池占据了 class 文件很大一部分数据，里面存放着各式各样的常量信息，包括数字和字符串常量、类和接口名、字段和方法名等等。

常量池实际上也是一个表，但是有三点需要特别注意：

表头给出的常量池大小比实际大 1，假设表头给出的值是 n，那么常量池的实际大小是 n-1

有效的常量池索引是 1~n–1。0 是无效索引，表示不指向任何常量

CONSTANT_Long_info 和 CONSTANT_Double_info 各占两个位置。也就是说，如果常量池中 存在这两种常量，实际的常量数量比 n–1 还要少，而且 1~n–1 的某些 数也会变成无效索引

:sparkles:常量池特点

- 常量池是 Class 文件中内容最为丰富的区域之一。常量池对于 Class 文件中的字段和方法解析也有着至关重要的作用
- 随着 Java 虚拟机的不断发展，常量池的内容也日渐丰富，可以说，**常量池是整个 Class 文件的基石**
- 在版本号之后，紧跟着的是常量池的数量，以及若干个常量池表项
- 常量池中常量的数量是不固定的，所以在常量池的入口需要放置一项 u2 类型的无符号数，代表常量池容量计数值(constant_pool_count)，

:notes:与 Java 中语言习惯不一样的是，常量池容量计数是从 1 而不是 0 开始的

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-yxec{color:#333;text-align:center;vertical-align:middle}
.tg .tg-hxm3{background-color:#F8F8F8;color:#333;text-align:center;vertical-align:middle}
.tg .tg-39k1{border-color:inherit;color:#333;font-family:"Times New Roman", Times, serif !important;font-size:100%;font-weight:bold;
  position:-webkit-sticky;position:sticky;text-align:center;top:-1px;vertical-align:middle;will-change:transform}
.tg .tg-dd3y{color:#333;font-weight:bold;position:-webkit-sticky;position:sticky;text-align:center;top:-1px;vertical-align:middle;
  will-change:transform}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-39k1">类型</th>
    <th class="tg-dd3y">名称</th>
    <th class="tg-dd3y">数量</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-yxec">u2（无符号数）</td>
    <td class="tg-yxec">constant_pool_count</td>
    <td class="tg-yxec">1</td>
  </tr>
  <tr>
    <td class="tg-hxm3">cp_info（表）</td>
    <td class="tg-hxm3">constant_pool</td>
    <td class="tg-hxm3">constant_pool_count-1</td>
  </tr>
</tbody>
</table>

由上表可见，Class 文件使用了一个前置的容量计数器(constant_pool_count)加若干个连续的数据项(constant_pool)的形式来描述常量池内容，我们把这一系列连续常量池数据称为**常量池集合**

:thinking:常量池中存的是什么东西

- **常量池表项**中，用于存放编译时期生成的各种**字面量**和**符号引用**，这部分内容将在类加载后进入方法区的**运行时常量池**中存放

### constant_pool_count（常量池计数器）

- 由于常量池的数量不固定，时长时短，所以需要放置两个字节来表示常量池容量计数值。

- 常量池容量计数值（u2 类型）：从 1 开始，表示常量池中有多少项常量。即 constant_pool_count=1 表示常量池中有 0 个常量项。

通常我们写代码时都是从 0 开始的，但是这里的常量池却是从 1 开始，因为它把第 0 项常量空出来了。这是为了满足后面某些指向常量池的索引值的数据在特定情况下需要表达“不引用任何一个常量池项目”的含义，这种情况可用索引值 0 来表示。

### constant_pool(常量池表)

constant_pool 是一种表结构，以 1 ~ constant_pool_count - 1 为索引。表明了后面有多少个常量项。

常量池主要存放两大类常量：字面量（Literal）和符号引用（Symbolic References）

它包含了 class 文件结构及其子结构中引用的所有字符串常量、类或接口名、字段名和其他常量。常量池中的每一项都具备相同的特征。第 1 个字节作为类型标记，用于确定该项的格式，这个字节称为 tag byte（标记字节、标签字节）。

由于常量池中存放的信息各不相同，所以各种常量的格式也不相同，常量数据的第一个字节是 tag，用来区分常量类型，下面是 Java 虚拟机规范给出的常量结构

```java
cp_info{
  u1 tag;
  u1 info[];
}
```

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-8azk{border-color:inherit;font-family:"Times New Roman", Times, serif !important;font-size:large;position:-webkit-sticky;
  position:sticky;text-align:center;top:-1px;vertical-align:middle;will-change:transform}
.tg .tg-diun{font-family:"Times New Roman", Times, serif !important;font-size:large;position:-webkit-sticky;position:sticky;
  text-align:center;top:-1px;vertical-align:middle;will-change:transform}
.tg .tg-3guk{font-family:"Times New Roman", Times, serif !important;font-size:large;text-align:center;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-8azk">类型</th>
    <th class="tg-diun">标志(或标识)</th>
    <th class="tg-diun">描述</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-3guk">CONSTANT_Utf8_info</td>
    <td class="tg-3guk">1</td>
    <td class="tg-3guk">UTF-8编码的字符串</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_Integer_info</td>
    <td class="tg-3guk">3</td>
    <td class="tg-3guk">整型字面量</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_Float_info</td>
    <td class="tg-3guk">4</td>
    <td class="tg-3guk">浮点型字面量</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_Long_info</td>
    <td class="tg-3guk">5</td>
    <td class="tg-3guk">长整型字面量</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_Double_info</td>
    <td class="tg-3guk">6</td>
    <td class="tg-3guk">双精度浮点型字面量</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_Class_info</td>
    <td class="tg-3guk">7</td>
    <td class="tg-3guk">类或接口的符号引用</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_String_info</td>
    <td class="tg-3guk">8</td>
    <td class="tg-3guk">字符串类型字面量</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_Fieldref_info</td>
    <td class="tg-3guk">9</td>
    <td class="tg-3guk">字段的符号引用</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_Methodref_info</td>
    <td class="tg-3guk">10</td>
    <td class="tg-3guk">类中方法的符号引用</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_InterfaceMethodref_info</td>
    <td class="tg-3guk">11</td>
    <td class="tg-3guk">接口中方法的符号引用</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_NameAndType_info</td>
    <td class="tg-3guk">12</td>
    <td class="tg-3guk">字段或方法的符号引用</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_MethodHandle_info</td>
    <td class="tg-3guk">15</td>
    <td class="tg-3guk">表示方法句柄</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_MethodType_info</td>
    <td class="tg-3guk">16</td>
    <td class="tg-3guk">标志方法类型</td>
  </tr>
  <tr>
    <td class="tg-3guk">CONSTANT_InvokeDynamic_info</td>
    <td class="tg-3guk">18</td>
    <td class="tg-3guk">表示一个动态方法调用点</td>
  </tr>
</tbody>
</table>

#### 字面量和符号引用

在对这些常量解读前，我们需要搞清楚几个概念。

常量池主要存放两大类常量：字面量（Literal）和符号引用（Symbolic References）。如下表

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:1px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:1px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-8azk{border-color:inherit;font-family:"Times New Roman", Times, serif !important;font-size:large;position:-webkit-sticky;
  position:sticky;text-align:center;top:-1px;vertical-align:middle;will-change:transform}
.tg .tg-9unp{border-color:inherit;font-size:large;position:-webkit-sticky;position:sticky;text-align:center;top:-1px;
  vertical-align:middle;will-change:transform}
.tg .tg-axi0{border-color:inherit;font-size:large;text-align:center;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-8azk">常量</th>
    <th class="tg-9unp">具体的常量</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-axi0" rowspan="2">字面量</td>
    <td class="tg-axi0">文本字符串</td>
  </tr>
  <tr>
    <td class="tg-axi0">声明为final的常量值</td>
  </tr>
  <tr>
    <td class="tg-axi0" rowspan="3">符号引用</td>
    <td class="tg-axi0">类和接口的全限定名</td>
  </tr>
  <tr>
    <td class="tg-axi0">字段的名称和描述符</td>
  </tr>
  <tr>
    <td class="tg-axi0">方法的名称和描述符</td>
  </tr>
</tbody>
</table>

##### 全限定名

`com/atguigu/test/Demo`这个就是类的全限定名，仅仅是把包名的`.`替换成`/`

:notes:为了使连续的多个全限定名之间不产生混淆，在使用时最后一般会加入一个`;`表示全限定名结束

##### 简单名称

简单名称是指**没有类型**和**参数修饰**的**方法或者字段名称**，上面例子中的类的 add()方法和 num 字段的简单名称分别是 add 和 num

##### 描述符

:sparkles:**描述符的作用是用来描述字段的数据类型、方法的参数列表（包括数量、类型以及顺序）和返回值**

根据描述符规则，基本数据类型(byte、char、double、float、int、long、short、boolean)以及代表无返回值的 void 类型都用一个大写字符来表示，而对象类型则用字符 L 加对象的全限定名表示

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-tdrq{font-size:x-large;text-align:center;vertical-align:top}
.tg .tg-8fkf{border-color:inherit;font-family:"Times New Roman", Times, serif !important;font-size:x-large;position:-webkit-sticky;
  position:sticky;text-align:center;top:-1px;vertical-align:top;will-change:transform}
.tg .tg-k8q5{font-size:x-large;position:-webkit-sticky;position:sticky;text-align:center;top:-1px;vertical-align:top;
  will-change:transform}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-8fkf">标志符</th>
    <th class="tg-k8q5">含义</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-tdrq">B</td>
    <td class="tg-tdrq">基本数据类型byte</td>
  </tr>
  <tr>
    <td class="tg-tdrq">C</td>
    <td class="tg-tdrq">基本数据类型char</td>
  </tr>
  <tr>
    <td class="tg-tdrq">D</td>
    <td class="tg-tdrq">基本数据类型double</td>
  </tr>
  <tr>
    <td class="tg-tdrq">F</td>
    <td class="tg-tdrq">基本数据类型float</td>
  </tr>
  <tr>
    <td class="tg-tdrq">I</td>
    <td class="tg-tdrq">基本数据类型int</td>
  </tr>
  <tr>
    <td class="tg-tdrq">J</td>
    <td class="tg-tdrq">基本数据类型long</td>
  </tr>
  <tr>
    <td class="tg-tdrq">S</td>
    <td class="tg-tdrq">基本数据类型short</td>
  </tr>
  <tr>
    <td class="tg-tdrq">Z</td>
    <td class="tg-tdrq">基本数据类型boolean</td>
  </tr>
  <tr>
    <td class="tg-tdrq">V</td>
    <td class="tg-tdrq">代表void类型</td>
  </tr>
  <tr>
    <td class="tg-tdrq">L</td>
    <td class="tg-tdrq">对象类型，比如：<br>Ljava/lang/Object;</td>
  </tr>
  <tr>
    <td class="tg-tdrq">[</td>
    <td class="tg-tdrq">数组类型，代表一维数组。比如：`double[][][] is [[[D</td>
  </tr>
</tbody>
</table>

:notes:用描述符来描述方法时，按照**先参数列表，后返回值的顺序描述**，参数列表按照参数的严格顺序放在一组小括号`()`之内，如方法`java.lang.String.toString()`的描述符为`()Ljava/lang/String`，方法`int abc(int[]x,int y)`的描述符为`([II)I`

虚拟机在加载 Class 文件时才会进行**动态链接**，也就是说，Class 文件中不会保存各个方法和字段的最终内存布局信息。因此，这些字段和方法的符号引用不经过转换是无法直接被虚拟机使用的。**当虚拟机运行时，需要从常量池中获得对应的符号引用，再在类加载过程中的解析阶段将其替换为直接引用，并翻译到具体的内存地址中**

:notebook:class 文件并不会明确表示类的最终内存布局，只有等到虚拟机运行，将 class 文件中的符号引用转换为直接引用，并翻译到具为具体的内存地址时才能知道一个类的内存布局

:vs:符号引用和直接引用的区别与关联

- 符号引用：符号引用以**一组符号**来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能无歧义地定位到目标即可。**符号引用与虚拟机实现的内存布局无关**，引用的目标并不一定已经加载到内存中
- 直接引用：直接引用可以是直接指**向目标的指针、相对偏移量或是一个能间接定位到目标的句柄。直接引用是与虚拟机实现的内存布局相关的，**同一个符号引用在不同虚拟机实例上翻译出来的直接引用一般不会相同。如果有了直接引用，那说明引用的目标必定已经存在于内存之中了

#### 常量类型和结构

常量池中每一项常量都是一个表，JdK1.7 之后共有 14 种不同的表结构数据

![](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/imgjvm-classfile-constantpooltype.png)

根据上图每个类型的描述我们也可以知道每个类型是用来描述常量池中哪些内容（主要是字面量、符号引用）的。比如:`CONSTANT_Integer_info`是用来描述常量池中字面量信息的，而且只是整型字面量信息

:notes:常量类型注意细节

- 标志为 15、16、18 的常量项类型是用来支持动态语言调用的（jdk1.7 时才加入的）
- CONSTANT_Long_info 和 CONSTANT_Double_info 结构表示 8 字节(long 和 double)的数值常量
- 在 Class 文件的常量池中，所有的 8 字节常量均占两个表成员(项)的空间，如果一个 CONSTANT_Long_info 或 CONSTANT_Double_info 结构的项在常量池表中的索引位 n，则常量池表中下一个可用项的索引位 n + 2，此时常量池表中索引为 n + 1，的项仍然有效但必须视为不可用的
- CONSTANT_NameAndType_info 结构用于表示字段或方法，但是和之前的 3 个结构不同，CONSTANT_NameAndType_info 结构没有指明该字段或方法所属的类或接口
- CONSTANT_InvokeDynamic_info 结构表示 invokedynamic 指令所用到的引导方法(bootstrap method)、引导方法所用到的动态调用名称(dynamic invocation name)、参数和返回类型，并可以给引导方法传入一系列称为静态参数（static argument）的常量

:notebook:总结

- 这 14 种表（或者常量项结构）的共同点是：表开始的第一位是一个 u1 类型的标志位（tag），代表当前这个常量项使用的是哪种表结构，即哪种常量类型
- 在常量池列表中，CONSTANT_Utf8_info 常量项是一种使用改进过的 UTF-8 编码格式来存储诸如文字字符串、类或者接口的全限定名、字段或者方法的简单名称以及描述符等常量字符串信息
- 这 14 种常量项结构还有一个特点是，其中 13 个常量项占用的字节固定，只有 CONSTANT_Utf8_info 占用字节不固定，其大小由 length 决定

:question:为什么`CONSTANT_Utf8_info`占用字节不固定

- 因为从常量池存放的内容可知，其存放的是字面量和符号引用，最终这些内容都会是一个字符串，这些字符串的大小是在编写程序时才确定，比如你定义一个类，类名可以取长取短，所以在没编译前，大小不固定，编译后，通过 utf-8 编码，就可以知道其长度

### 常量池总结

:notebook:总结

- 常量池：可以理解为 Class 文件之中的资源仓库，它是 Class 文件结构中与其他项目关联最多的数据类型（后面的很多数据类型都会指向此处），也是占用 Class 文件空间最大的数据项目之一
- 常量池中为什么要包含这些内容？Java 代码在进行 Javac 编译的时候，并不像 C 和 C++那样有**连接**这一步骤，而是在虚拟机加载 C1ass 文件的时候进行动态链接。也就是说，在 Class 文件中不会保存各个方法、字段的最终内存布局信息，因此这些字段、方法的符号引用不经过运行期转换的话无法得到真正的内存入口地址，也就无法直接被虚拟机使用。
- 当虚拟机运行时，需要从常量池获得对应的符号引用，再在类创建时或运行时解析、翻译到具体的内存地址之中。关于类的创建和动态链接的内容，在虚拟机类加载过程时再进行详细讲解

## 访问标志

:sparkles:访问标识（`access_flag`、访问标志，访问标记）在常量池后，该标记使用两个字节表示，用于识别一些类或者接口层次的访问信息，这是一个 16 位的`bitmask`

包括：这个 Class 是类还是接口；是否定义为 public 类型；是否定义为 abstract 类型；如果是类的话，是否被声明为 final 等

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-y4e2{font-size:medium;position:-webkit-sticky;position:sticky;text-align:center;top:-1px;vertical-align:middle;
  will-change:transform}
.tg .tg-rx7n{font-size:medium;text-align:center;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-y4e2">标志名称</th>
    <th class="tg-y4e2">标志值</th>
    <th class="tg-y4e2">含义</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-rx7n">ACC_PUBLIC</td>
    <td class="tg-rx7n">0x0001</td>
    <td class="tg-rx7n">标志为public类型</td>
  </tr>
  <tr>
    <td class="tg-rx7n">ACC_FINAL</td>
    <td class="tg-rx7n">0x0010</td>
    <td class="tg-rx7n">标志被声明为final,只有类可以设置</td>
  </tr>
  <tr>
    <td class="tg-rx7n">ACC_SUPER</td>
    <td class="tg-rx7n">0x0020</td>
    <td class="tg-rx7n">标志允许使用invokespecial字节码指令的新语义，<br>JDK1.0.2之后编译出来的类的这个标志默认为真。<br>（使用增强的方法调用父类方法）</td>
  </tr>
  <tr>
    <td class="tg-rx7n">ACC_INTERFACE</td>
    <td class="tg-rx7n">0x0200</td>
    <td class="tg-rx7n">标志这是一个接口</td>
  </tr>
  <tr>
    <td class="tg-rx7n">ACC_ABSTRACT</td>
    <td class="tg-rx7n">0x0400</td>
    <td class="tg-rx7n">标志当前类是否为abstract类型，<br>对于接口或者抽象类来说此标志值为真，其他类型为假</td>
  </tr>
  <tr>
    <td class="tg-rx7n">ACC_SYNTHETIC</td>
    <td class="tg-rx7n">0x1000</td>
    <td class="tg-rx7n">标志此类并非由用户代码产生<br>（即：由编译器产生的类，没有源码对应）</td>
  </tr>
  <tr>
    <td class="tg-rx7n">ACC_ANNOTATION</td>
    <td class="tg-rx7n">0x2000</td>
    <td class="tg-rx7n">标志这是一个注解</td>
  </tr>
  <tr>
    <td class="tg-rx7n">ACC_ENUM</td>
    <td class="tg-rx7n">0x4000</td>
    <td class="tg-rx7n">标志这是一个枚举</td>
  </tr>
</tbody>
</table>

:sparkles:访问标志特点

- 类的访问权限通常为 ACC\_开头的常量
- 每一种类型的表示都是通过设置访问标记的 32 位中的特定位来实现的。比如，若是 public final 的类，则该标记为`ACC_PUBLIC | ACC_FINAL`C 中常用的手段
- 使用`ACC_SUPER`可以让类更准确地定位到父类的方法`super.method()`，现代编译器都会设置并且使用这个标记

:notes:访问标志注意点

- 带有 ACC_INTERFACE 标志的 class 文件表示的是接口而不是类
  - 如果一个 class 文件被设置了`ACC_INTERFACE`标志，那么同时也得设置`ACC_ABSTRACT`标志。同时它不能再设置`ACC_FINAL`、`ACC_SUPER `或`ACC_ENUM`标志
  - 如果没有设置`ACC_INTERFACE`标志，那么这个 class 文件可以具有上表中除`ACC_ANNOTATION`外的其他所有标志。当然`ACC_FINAL`和`ACC_ABSTRACT`这类互斥的标志除外。这两个标志不得同时设置
- ACC_SUPER 标志用于确定类或接口里面的`invokespecial`指令使用的是哪一种执行语义。针对 Java 虚拟机指令集的编译器都应当设置这个标志。对于 Java SE 8 及后续版本来说，无论 class 文件中这个标志的实际值是什么，也不管 class 文件的版本号是多少，Java 虚拟机都认为每个 class 文件均设置了 ACC_SUPER 标志
  - ACC_SUPER 标志是为了向后兼容由旧 Java 编译器所编译的代码而设计的。目前的 ACC_SUPER 标志在由 JDK1.0.2 之前的编译器所生成的 access_flags 中是没有确定含义的，如果设置了该标志，那么 0racle 的 Java 虚拟机实现会将其忽略
- ACC_SYNTHETIC 标志意味着该类或接口是由编译器生成的，而不是由源代码生成的
- 注解类型必须设置 ACC_ANNOTATION 标志。如果设置了 ACC_ANNOTATION 标志，那么也必须设置 ACC_INTERFACE 标志
- ACC_ENUM 标志表明该类或其父类为枚举类型

## 索引常量池

在访问标记之后，会指定该类的类别、父类类别以及实现的接口，格式如下：

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-3nt7{border-color:inherit;font-family:"Times New Roman", Times, serif !important;font-size:x-large;position:-webkit-sticky;
  position:sticky;text-align:center;top:-1px;vertical-align:middle;will-change:transform}
.tg .tg-qhri{border-color:inherit;font-family:"Times New Roman", Times, serif !important;font-size:x-large;text-align:center;
  vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-3nt7">长度</th>
    <th class="tg-3nt7">含义</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-qhri">u2</td>
    <td class="tg-qhri">this_class</td>
  </tr>
  <tr>
    <td class="tg-qhri">u2</td>
    <td class="tg-qhri">super_class</td>
  </tr>
  <tr>
    <td class="tg-qhri">u2</td>
    <td class="tg-qhri">interfaces_count</td>
  </tr>
  <tr>
    <td class="tg-qhri">u2</td>
    <td class="tg-qhri">interfaces[interfaces_count]</td>
  </tr>
</tbody>
</table>

这三项数据用来确定这个类的继承关系

- 类索引用于确定这个类的全限定名
- 父类索引用于确定这个类的父类的全限定名。由于 Java 语言不允许多重继承，所以父类索引只有一个，除了 java.lang.Object 之外，所有的 Java 类都有父类，因此除了 java.lang.Object 外，所有 Java 类的父类索引都不为 `0`
- 接口索引集合就用来描述这个类实现了哪些接口，这些被实现的接口将按 implements 语句（如果这个类本身是一个接口，则应当是 extends 语句）后的接口顺序从左到右排列在接口索引集合中

### 类索引与父类索引

类访问标志之后是两个 u2 类型的常量池索引，分别给出**当前类名**和**超类名**

class 文件存储的类名类似完全限定名，但是把点换成了斜线，Java 语言规范把这种名字叫作二进制名(binary names)。因为每个类都有名字，所以 thisClass 必须是有效的常量池索引

除 java.lang.Object 之外，其他类都有超类，所以 superClass 只在 Object.class 中是 0，在其他 class 文件中必须是有效的常量池索引

#### this_class（类索引）

2 字节无符号整数，指向常量池的索引。它提供了类的全限定名，如 com/atguigu/java1/Demo。this_class 的值必须是对常量池表中某项的一个有效索引值。常量池在这个索引处的成员必须为 CONSTANT_Class_info 类型结构体，该结构体表示这个 class 文件所定义的类或接口。

#### super_class（父类索引）

2 字节无符号整数，指向常量池的索引。它提供了当前类的父类的全限定名。如果我们没有继承任何类，其默认继承的是 java/lang/object 类。同时，由于 Java 不支持多继承，所以其父类只有一个

super_class 指向的父类不能是 final

### 接口索引表

类和超类索引后面是接口索引表，表中存放的也是常量池索引集合，它提供了一个符号引用到所有已实现的接口

由于一个类可以实现多个接口，因此需要以数组形式保存多个接口的索引，表示接口的每个索引也是一个指向常量池的 CONSTANT_Class（这里就必须是接口，而不是类）

#### interfaces_count（接口计数器）

interfaces_count 项的值表示当前类或接口的直接超接口数量

#### interfaces[]（接口索引集合）

interfaces[]中每个成员的值必须是对常量池表中某项的有效索引值，它的长度为 interfaces_count。每个成员 interfaces[i]必须为 CONSTANT_Class_info 结构，其中 0 <= i < interfaces_count。在 interfaces[]中，各成员所表示的接口顺序和对应的源代码中给定的接口顺序（从左至右）一样，即 interfaces[0]对应的是源代码中最左边的接口。

## 字段和方法表

接口索引表之后是字段表和方法表，分别存储字段和方法信息。字段和方法的基本结构大致相同，差别仅在于属性表，java 虚拟机规范中给出的字段结构定义如下：

```java
field_info{
  u2 access_flags; //访问标志
  u2 name_index;	//字段名
  u2 descriptor_index;	//字段描述符
  u2 attributes_count;	//属性表中的元素个数
  attribute_info attributes[attrbutes_count]; //属性表信息
}
```

和类一样，字段和方法也有自己的访问标志。访问标志之后是 一个常量池索引，给出字段名或方法名，然后又是一个常量池索 引，给出字段或方法的描述符，最后是属性表。

### 字段表集合

fields 用于描述接口或类中声明的变量。字段（field）包括类级变量以及实例级变量，但是不包括方法内部、代码块内部声明的局部变量。

字段叫什么名字、字段被定义为什么数据类型，这些都是无法固定的，只能引用常量池中的常量来描述。

它指向常量池索引集合，它描述了每个字段的完整信息。比如字段的标识符、访问修饰符（public、private 或 protected）、是类变量还是实例变量（static 修饰符）、是否是常量（final 修饰符）等。

:notes:

- 字段表集合中不会列出从父类或者实现的接口中继承而来的字段，但有可能列出原本 Java 代码之中不存在的字段。譬如在内部类中为了保持对外部类的访问性，会自动添加指向外部类实例的字段。

- 在 Java 语言中字段是无法重载的，两个字段的数据类型、修饰符不管是否相同，都必须使用不一样的名称，但是对于字节码来讲，如果两个字段的描述符不一致，那字段重名就是合法的

#### 字段计数器

fields_count 的值表示当前 class 文件 fields 表的成员个数。使用两个字节来表示。

fields 表中每个成员都是一个 field_info 结构，用于表示该类或接口所声明的所有类字段或者实例字段，不包括方法内部声明的变量，也不包括从父类或父接口继承的那些字段。

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-8azk{border-color:inherit;font-family:"Times New Roman", Times, serif !important;font-size:large;position:-webkit-sticky;
  position:sticky;text-align:center;top:-1px;vertical-align:middle;will-change:transform}
.tg .tg-yrj0{font-size:large;position:-webkit-sticky;position:sticky;text-align:center;top:-1px;vertical-align:middle;
  will-change:transform}
.tg .tg-4gcf{font-size:large;text-align:center;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-8azk">标志名称</th>
    <th class="tg-yrj0">标志值</th>
    <th class="tg-yrj0">含义</th>
    <th class="tg-yrj0">数量</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-4gcf">u2</td>
    <td class="tg-4gcf">access_flags</td>
    <td class="tg-4gcf">访问标志</td>
    <td class="tg-4gcf">1</td>
  </tr>
  <tr>
    <td class="tg-4gcf">u2</td>
    <td class="tg-4gcf">name_index</td>
    <td class="tg-4gcf">字段名索引</td>
    <td class="tg-4gcf">1</td>
  </tr>
  <tr>
    <td class="tg-4gcf">u2</td>
    <td class="tg-4gcf">descriptor_index</td>
    <td class="tg-4gcf">描述符索引</td>
    <td class="tg-4gcf">1</td>
  </tr>
  <tr>
    <td class="tg-4gcf">u2</td>
    <td class="tg-4gcf">attributes_count</td>
    <td class="tg-4gcf">属性计数器</td>
    <td class="tg-4gcf">1</td>
  </tr>
  <tr>
    <td class="tg-4gcf">attribute_info</td>
    <td class="tg-4gcf">attributes</td>
    <td class="tg-4gcf">属性集合</td>
    <td class="tg-4gcf">attributes_count</td>
  </tr>
</tbody>
</table>

#### 字段表

##### 字段表访问标识

一个字段可以被各种关键字去修饰，比如：作用域修饰符（public、private、protected）、static 修饰符、final 修饰符、volatile 修饰符等等。因此，其可像类的访问标志那样，使用一些标志来标记字段。字段的访问标志有如下这些：

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-8azk{border-color:inherit;font-family:"Times New Roman", Times, serif !important;font-size:large;position:-webkit-sticky;
  position:sticky;text-align:center;top:-1px;vertical-align:middle;will-change:transform}
.tg .tg-yrj0{font-size:large;position:-webkit-sticky;position:sticky;text-align:center;top:-1px;vertical-align:middle;
  will-change:transform}
.tg .tg-4gcf{font-size:large;text-align:center;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-8azk">标志名称</th>
    <th class="tg-yrj0">标志值</th>
    <th class="tg-yrj0">含义</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-4gcf">ACC_PUBLIC</td>
    <td class="tg-4gcf">0x0001</td>
    <td class="tg-4gcf">字段是否为public</td>
  </tr>
  <tr>
    <td class="tg-4gcf">ACC_PRIVATE</td>
    <td class="tg-4gcf">0x0002</td>
    <td class="tg-4gcf">字段是否为private</td>
  </tr>
  <tr>
    <td class="tg-4gcf">ACC_PROTECTED</td>
    <td class="tg-4gcf">0x0004</td>
    <td class="tg-4gcf">字段是否为protected</td>
  </tr>
  <tr>
    <td class="tg-4gcf">ACC_STATIC</td>
    <td class="tg-4gcf">0x0008</td>
    <td class="tg-4gcf">字段是否为static</td>
  </tr>
  <tr>
    <td class="tg-4gcf">ACC_FINAL</td>
    <td class="tg-4gcf">0x0010</td>
    <td class="tg-4gcf">字段是否为final</td>
  </tr>
  <tr>
    <td class="tg-4gcf">ACC_VOLATILE</td>
    <td class="tg-4gcf">0x0040</td>
    <td class="tg-4gcf">字段是否为volatile</td>
  </tr>
  <tr>
    <td class="tg-4gcf">ACC_TRANSTENT</td>
    <td class="tg-4gcf">0x0080</td>
    <td class="tg-4gcf">字段是否为transient</td>
  </tr>
  <tr>
    <td class="tg-4gcf">ACC_SYNCHETIC</td>
    <td class="tg-4gcf">0x1000</td>
    <td class="tg-4gcf">字段是否为由编译器自动产生</td>
  </tr>
  <tr>
    <td class="tg-4gcf">ACC_ENUM</td>
    <td class="tg-4gcf">0x4000</td>
    <td class="tg-4gcf">字段是否为enum</td>
  </tr>
</tbody>
</table>

##### 描述符索引

描述符的作用是用来描述字段的数据类型、方法的参数列表（包括数量、类型以及顺序）和返回值。根据描述符规则，基本数据类型（byte，char，double，float，int，long，short，boolean）及代表无返回值的 void 类型都用一个大写字符来表示，而对象则用字符 L 加对象的全限定名来表示，如下所示：

<style type="text/css">
.tg  {border:none;border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:0px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-95sm{background-color:#B9C9FE;color:#039;font-size:x-small;font-weight:bold;text-align:center;vertical-align:top}
.tg .tg-qkmr{background-color:#E8EDFF;color:#669;font-size:x-small;font-weight:bold;text-align:center;vertical-align:top}
.tg .tg-fbwb{background-color:#E8EDFF;color:#669;font-size:100%;font-weight:bold;text-align:center;vertical-align:top}
</style>
<table class="tg">
<tbody>
  <tr>
    <td class="tg-95sm"><span style="font-weight:400;color:#039;background-color:#B9C9FE">标志符</span></td>
    <td class="tg-95sm"><span style="font-weight:400;color:#039;background-color:#B9C9FE">含义</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">B</span></td>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">基本数据类型byte</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">C</span></td>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">基本数据类型char</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">D</span></td>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">基本数据类型double</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">F</span></td>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">基本数据类型float</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">I</span></td>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">基本数据类型int</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">J</span></td>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">基本数据类型long</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">S</span></td>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">基本数据类型short</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">Z</span></td>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">基本数据类型boolean</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">V</span></td>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">代表void类型</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">L</span></td>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">对象类型，比如：</span><br><span style="color:#669;background-color:#E8EDFF">Ljava/lang/Object;</span></td>
  </tr>
  <tr>
    <td class="tg-qkmr"><span style="color:#669;background-color:#E8EDFF">[</span></td>
    <td class="tg-fbwb"><span style="color:#669;background-color:#E8EDFF">数组类型，代表一维数组。</span><br><span style="color:#669;background-color:#E8EDFF">比如：`double[][][] is [[[D`</span></td>
  </tr>
</tbody>
</table>

##### 属性表集合

一个字段还可能拥有一些属性，用于存储更多的额外信息。比如初始化值、一些注释信息等。属性个数存放在 attribute_count 中，属性具体内容存放在 attributes 数组中。

```java
// 以常量属性为例，结构为：
ConstantValue_attribute{
	u2 attribute_name_index;
	u4 attribute_length;
  u2 constantvalue_index;
}
```

说明：对于常量属性而言，attribute_length 值恒为 2。

### 方法表集合

methods：指向常量池索引集合，它完整描述了每个方法的签名。

- 在字节码文件中，每一个 method_info 项都对应着一个类或者接口中的方法信息。比如方法的访问修饰符（public、private 或 protected），方法的返回值类型以及方法的参数信息等。

- 如果这个方法不是抽象的或者不是 native 的，那么字节码中会体现出来。

- 一方面，methods 表只描述当前类或接口中声明的方法，不包括从父类或父接口继承的方法。另一方面，methods 表有可能会出现由编译器自动添加的方法，最典型的便是编译器产生的方法信息（比如：类（接口）初始化方法<clinit>()和实例初始化方法`<init>()`）。

## 附录

[类文件结构](https://doocs.github.io/jvm/07-class-structure.html)
[深入理解 JVM 之 Java 字节码（.class）文件详解](https://blog.csdn.net/weelyy/article/details/78969412)
[官方文档](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html)
[JVM Class 详解之一](https://developer.aliyun.com/article/7241?spm=a2c6h.13262185.0.0.46833694r5uY9M)
