---
title: make
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: C
tags: C
keywords: make
excerpt: make 作为常见的 C 工具，此前一直有听闻，但是都没有深入学习，想要在这里记录一下
date: 2022-04-29 21:46:38
thumbnailImage:
---
<!-- toc -->

函数指针

http://c.biancheng.net/view/228.html

C语言结构体中定义函数指针

https://blog.csdn.net/qq_44884619/article/details/89178487

# 编译器gcc

Linux系统下对C程序的编译器

程序的编译共分为四个阶段，由`.c`到`可执行程序`的过程如下:

1. 预编译(预处理)
2. 编译
3. 汇编
4. 链接

## 1.直接生成可执行程序

```shell
gcc file.c	#会直接编译程序并在当前目录下生成对应的可执行文件，a.out
gcc file.c -o ExecFile	#会在当前目录下生成名为ExecFile的可执行文件
```

## 2.分步骤编译

```shell
gcc -E hello.c -o hello.i	#预处理阶段，就是将头文件宏定义展开
gcc -S hello.i -o hello.s	#编译阶段，将 *.i 文件编译为 汇编文件
gcc -c hello.s -o hello.o	#汇编阶段，将汇编文件变成二进制文件
gcc hello.o -o hello		#链接阶段，将当前二进制文件变为可执行文件
```

# Makefile

`make`与Makefile文件的关系,类似于`docker build`与Dockerfile文件的关系,

## 1.什么是make

make是个命令存放在`/usr/bin下`，是个可执行程序,用来解析Makefile文件的命令，

## 2.什么是Makefile

Makefile是一个文件，这个文件中描述了相应程序的编译规则。在执行make命令的时候，make命令会在当前目录下找Makefile文件，根据Makefile文件中的规则，编译对应的程序

## 3.为什么使用Makefile

1. 简化编译程序的时候输入命令，编译的时候只需要输入make命令即可
2. 可以节省编译时间，提高编译效率(编译过一次后，下次再编译，只会编译修改过时间戳的文件)

## 4.makefile语法规则

```makefile
目标文件:依赖文件列表
<Tab>命令列表#前面又一个tab键
```

### 4.1目标文件

通常是要产生的文件名称，目标可以是可执行文件或其他`obj`文件，也可是一个动作的名称

### 4.2依赖文件

是用来输入从而产生目标的文件，一个目标通常有几个依赖文件(也可能一个文件也不依赖)

### 4.3命令

make执行的动作，一个规则可以含多个命令也可以不含命令，当有多个命令时，每个命令占一行

### 众多等号的作用

```makefile
#给变量赋值(浅拷贝，Var1=Var2,Var1的改变会带动Var2的改变)，
#变量最终的值是最后一次的改变值
=
#给变量赋值，并且变量最终的值不随Var2而改变
:=
#如果当前变量未被赋值，那么就对此变量赋值
?=
#可以给变量追加内容
+=
```


### example

```makefile
main:main.c main.h
	gcc main.c -o main
clean:
	rm -rf main
```

## 5.Make Format

```shell
make [-f file] [targets] 
#通常用法
make
```

### -f file

make默认在工作目录中寻找名为`GNUmakefile`,`makefile`,`Makefile`的文件作为make命令的输入文件，如果当前目录下不存在上述文件，将需要使用-f 指定makefile文件，与Dockerfile类似

### targets

若使用make命令时没有指定目标，则make工具默认会实现makefile文件内的第一个目标，然后退出，指定了make工具要实现的目标，目标可以是一个或多个(多个目标之间用空格隔开)

### 伪目标

makefile有一种特殊的目标-伪目标，一般的目标名都是要生成的文件，而伪目标不代表真正的文件名，在执行`make`命令的时候通过指定这个伪目标来执行其所在规则的定义的命令。

使用伪目标的主要是为了避免Makefile中定义的只执行命令的名称和工作目录下的实际文件出现名字冲突，这时候就需要编写一个规则来声明这个只执行命令的名称是一个伪目标并不是用来创建文件的。如果未声明此字段为伪目标，则一旦当前目录拥有此文件，makefile中此目标下的所有命令均不会再执行（因为当前文件一直时最新的，从未修改过）

### example

```makefile
.PHONY:clean	#将clean定义为伪目标
main:main.o sum.o sub.o
	gcc main.o sum.o sub.o -o main
sub.o:sub.c
	gcc -c sub.c -o sub.o
sum.o:sum.c
	gcc -c sum.c -o sum.o
main.o:main.c
	gcc -c main.c -o main.o
clean:
	@rm -rf *.o main a.out
#---------------------------
make
```

- 并没有指定make的targets所以默认实现第一个目标。由于第一个目标依赖于*.o文件，而各个依赖的文件尚不存在，所以会在makefile文件中找到以依赖文件为目标的部分，并执行其下的命令，最终生成依赖文件
- 声明clean为伪目标以后不管当前目录下是否存在名字为`clean`文件,输入`make clean`最终都会执行makefile下的命令

## 6.makefile变量

makefile中的变量类似于C语言中的宏，当makefile被make工具解析时，其中的变量会被展开。变量的作用：

- 保存文件名列表
- 保存文件目录列表
- 保存编译器名
- 保存编译参数
- 保存编译的输出

### 变量的分类

- 自定义变量
  - 在makefile中国定义的变量，make工具传给makefile的变量
- 系统环境变量
  - make工具解析makefile前，读取系统环境变量并设置为makefile的变量
- 预定义变量(自动变量)

#### 自定义变量

```makefile
定义变量：
	变量名=变量值
引用变量：
	$(变量名)或${变量名}
```

> :warning:注：
>
> - 变量是大小写敏感的
> - 变量一般都在makefile的头部定义
> - 变量几乎可在makefile的任何地方使用(同shell)
> - 变量名是可以以数字开头的

#### 系统环境变量

make工具会拷贝系统的环境变量并将其设置为makefile的变量，在makefile中可直接读取或修改拷贝后的变量,一般并不适用

#### 模式选择

`%`表示通配符，`a.%.c`表示的意思就是以`a.`开头并且以`.c`结束的文件，当%出现在目标中的时候，目标中`%`所代表的值决定了依赖中`%`值,通常和预定义变量结合在一起使用

#### 预定义的变量

makefile中有许多预定义变量，这些变量具有特殊的含义，可在makefile中直接使用

```makefile
@	make执行时，不把shell命令打印出来，仅打印最终结果
$@	目标名
$<	依赖文件列表中的第一个文件
$^	依赖文件列表中除去重复文件的部分
$+ 	依赖文件，但是并不去重
$*	目标模式中%匹配到的整个内容, 如：模式a.%.c去匹配test/a.test.c ,$*就是test/a.test.c
CC	C编译器的名称，默认为cc
CFLAGS	C编译器的选项
#---------------------------以上较为常用
AR	归档维护程序的程序名,默认为ar
ARFLAGS	归档维护程序的选项
AS	编译程序的名称，默认为as
ASFLAGS	汇编程序的选项
CPP	c预编译器的名称，默认值为$(cc) -E
CPPFLAGS	C预编译的选项
CXX	C++编译器的名称。默认为g++
CXXFLAGS	C++编译器的选项
```





















