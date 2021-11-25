---
title: Maven 工具的使用
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: Java
tags: 项目
keywords: Maven
excerpt: "其实一个复杂的 Java 项目，总是需要很多其他人已经完成的组件（避免重复造轮子），在前端中可以使用 npm 或 yarn 等包管理工具添加依赖，由于我主要使用 maven 工具，所以主要介绍 maven 工具下的开发流程"
---
# 第一章：Maven简介

明白`Maven`的重要性，还需要先从<font style="color:red;font-weight:bold">软件工程</font>这件事说起

## 1.1 软件工程

软件工程，顾名思义就是软件的工程，要了解这个词就需要分别了解两个词`软件`和`工程`。

> :question:什么是工程？
>
> - 各行业的从业人员通过总结规律或者方法，以最短的时间和人力、物力来做出高效可靠的东西；且这种规律或方法是<font style="color:red;font-weight:bold">可复用</font>的
>
> :sparkles:什么是软件工程?
>
> - 为了能够实现<font style="color:red;font-weight:bold">软件的流水线生产</font>，软件工程是**设计和构建软件**时的<font style="color:red;font-weight:bold">一种规范和工程化的方法</font>。

## 1.2 软件工程的工作流程

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/软件工程的工作流程.png)

> :notes:上述过程需要**重复多次迭代**，在大型工程中<font style="color:red;font-weight:bold">构建项目比较复杂</font>，有许多**配置文件**、**jar文件**、**多个子项目等**，使用人力尽心管理——**费时费力**
>
> :thinking:传统开发项目(没有使用自动化管理工具)存在的问题
>
> 1. 项目**模块繁杂**，管理不便
> 2. **三方依赖众多**，资源查询不便
> 3. 控制各个 jar 包的**版本**，管理不便
> 4. 查询 jar 包的依赖，管理不便
>
> :tipping_hand_man:Maven可以让我们从上面的工作中解脱出来，<font style="color:red;font-weight:bold">Maven是自动化构建工具</font>

## 1.3 Maven概述

> :thinking:什么是Maven?​
>
> - Maven 是 Apache 软件基金会组织维护的一款**自动化构建工具**,专注服务于<font style="color:red;font-weight:bold"> Java 平台的项目构建和依赖管理</font>
>
> :grey_question:Maven能干什么？
>
> 1. Maven可以管理 jar 文件
> 2. 自动下载 jar 和它的文档，源代码
> 3. 管理 jar 包的依赖
> 4. 管理需要的 jar 的版本
> 5. 帮助编译程序，将 java 编译为 class
> 6. 帮助测试代码是否正确
> 7. 帮助打包文件，形成 jar 文件，或者 war 文件
> 8. 帮助部署项目
>
> :older_man:改进项目的开发和管理，需要Maven，快快用起来吧. :-)

构建：是面向过程的(从开始到结尾的多个步骤)，涉及到多个环节的协同工作

构建过程中各个环节：清理、编译、测试、报告、打包、安装、部署

1. 清理：删除以前的编译结果，为重新编译做好准备
2. 编译：将Java源程序编译为字节码文件
3. 测试：针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性
4. 报告：在每一次测试后以标准的格式记录和展示测试结果
5. 打包：将一个包含诸多文件的工程封装为一个压缩文件用于安装或部署。Java工程对应jar包，Web工程对应war包
6. 安装：在Maven环境下特指将打包的结果——jar包或war包安装到本地仓库中
7. 部署：将打包的结果部署到远程仓库或将war包部署到服务器上运行

## 1.4 Maven核心概念

Maven能够实现自动化构建是和它的内部原理分不开的，主要从Maven的九个核心概念入手，查看Maven是如何实现自动化构建

1. POM：POM是一个文件，文件名称为`pom.xml`，pom翻译过来叫做**项目对象模型**。maven把一个项目当作一个模型使用，控制maven构建项目的过程，管理jar依赖
2. 约定的目录结构：Maven项目的目录和文件的位置都是规定的
3. 坐标：是一个唯一的字符串，用来表示资源的
4. 依赖管理：管理你的项目可以使用的jar文件
5. 仓库管理：资源存放的位置
6. 生命周期：maven工具构建项目的过程，就是生命周期
7. 插件和目标：执行maven构建的时候用的工具是插件
8. 继承(后期学习)
9. 聚合(后期学习)

> :older_man:Maven的使用，是先难后易的，难的是Maven的命令,及完成Maven的使用。**在idea中使用鼠标就能操作**

## 1.5 安装Maven环境

Windows 环境开发比较方便，所以主要介绍如何在 Windows 下安装 Maven

### 1.5.1 Windows下安装

> :notes:Maven安装步骤
>
> 1. 从maven的官网下载maven的安装包`apache-maven-3.3.9-bin.zip`
> 2. 解压安装包，<font style="color:red;font-weight:bold">解压到非中文目录</font>
> 3. 配置环境变量（指定maven工具安装目录/bin）
> 4. 验证是否安装成功,命令行中执行`mvn -v`:notes:(需要配置Java_HOME)，用来指定JDK路径

> :sparkles:Maven软件目录结构
>
> 1. `bin` ：存放许多执行程序(如：mvn.cmd)
> 2. `conf` ：maven工具本身的配置文件settings.xml

> :notes:建议使用3.3.9版本，直接适应用JDK1.8,避免最新版Maven出现

### 1.5.1 Linux下安装

目前Maven主要维护的版本有：

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210320123556811.png)

> :notes:使用官方的源码包及逆行安装
>
> :one:下载
>
> - 通过官网下载相应的`apache-maven-3.3.9-bin.tar.gz`压缩包
>
> :two:解压
>
> - `tar zxcf apache-maven-3.3.9-bin.tar.gz`
>
> :three:安装
>
> - 编辑 `/etc/profile`
> - 配置
>
> ```shell
> // 安装目录
> export MAVEN_HOME=/home/homer/Apache-maven/apache-maven-3.3.9 
> export PATH=${MAVEN_HOME}/bin:${PATH}
> ```
>
> :four:使配置生效
>
> - `source /etc/profile`
>
> :five:验证
>
> - 输入`mvn -v`查看是否显示版本信息

> :point_right:[点击查看Maven的版本信息](https://maven.apache.org/docs/history.html)
>
> :point_right:更详细的步骤，[点击查看官方安装步骤](https://maven.apache.org/install.html)

## 1.6 配置阿里云环境

国内访问，总是不能友好的访问外网的资源，所以需要配置阿里云地址

```xml
<mirrors>
	<mirror>
    	<id>alimaven</id>
        <mirrorOf>central</mirrorOf>
        <name>aliyun maven</name>
   <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
 	</mirror>
    <!-- 中央仓库1 -->
    	<mirror>
        	<id>repo1</id>
            <mirrorOf>central</mirrorOf>
            <name>Human Readable Name for this Mirror.</name>
            <url>http://repo1.maven.org/maven2/</url>
        </mirror>
        <!-- 中央仓库2 -->
        <mirror>
            <id>repo2</id>
            <mirrorOf>central</mirrorOf>
            <name>Human Readable Name for this Mirror.</name>
            <url>http://repo2.maven.org/maven2/</url>
        </mirror>
</mirrors>
```

:notes:`settings.xml`文件需要格式化,[xml文件格式化网站](https://c.runoob.com/front-end/710)

:notes:要学会查看错误，maven 的错误多是<font style="color:red;font-weight:bold">某个插件没有安装，所以增加上就可以了</font>

:notes:安装完成之后，一定要在项目中`mvn install`进行测试，并安装 mvn 运行需要的一些插件

官网推荐配置，

https://maven.apache.org/install.html

下载java

https://www.cnblogs.com/wjup/p/11041274.html

https://www.java.com/zh-CN/download/linux_manual.jsp

# 第二章：Maven的核心概念



## 2.1 约定的Maven工程目录结构

每一个maven项目在磁盘中都是一个文件夹

```
Hello(根目录，也就是工程名)
	|---src(源代码)
	|---|---main(主程序java代码和配置文件)
	|---|---|---java(程序包和包中的java文件)
	|---|---|---resources(java程序中要使用的配置文件)
	|---|---test(放测试程序代码和文件的)
	|---|---|---java(测试程序包和包中的java文件)
	|---|---|---resources(测试java程序中要使用的配置文件)
	|---pom.xml(Maven的核心文件,Maven项目必须有)
```

项目常见完成,使用`mvn compile`编译src/main目录下的所有java文件,检查 maven 是否已经管理项目?

成功:

会下载很多插件,并在项目的根目录下生成`target`目录,Maven编译的java程序,所有的class文件都放在target目录中

> 1. 为什么要下载
>
>   - maven工具执行的操作需要很多插件(java类——jar文件)
>
> 2. 下载什么东西？
>    - jar文件，一些插件
> 3. 下载的东西放在那里了?
>    - C:\Users\（登录操作系统的用户名）Administrator\.m2\repository

> :notes: https://repo.maven.apache.org ：中央仓库的地址

设置本机存放资源的目录位置(设置本机仓库):

1. 修改maven的配置文件， maven安装目录/conf/settings.xml
    先备份 settings.xml
2. 修改 \<localRepository>  指定你的目录（不要使用中文目录）



## 2.2 仓库

maven是如何做到自动帮助用户托管项目，项目依赖的jar包从哪儿获取呢？

这一切都是由于存在 maven 仓库的概念。   

### 2.2.1 仓库是什么

Maven核心程序仅仅定义了自动化构建项目的生命周期，但具体的构建工作是由特定的构件完成的。而且为了提高构建的效率和构件复用， maven把所有的构件统一存储在某一个位置，这个位置就叫做仓库。  

> :notes:仓库是存放 Maven 及项目正常运行所需的 Jar 包的位置,主要存放的有:
>
> - maven使用的插件(各种jar)
> - 项目使用的jar（第三方的工具）

### 2.2.2 仓库的分类



| 仓库的分类 |                 作用                 |
| :--------: | :----------------------------------: |
|  本地仓库  | 本地计算机上的文件夹,存放各种 jar 包 |
|  远程仓库  |                                      |
|            |                                      |
|            |                                      |

仓库共分为两种：本地仓库和远程仓库

本地仓库：本地计算机上的文件夹，存放各种jar

远程仓库:在互联网上，使用网络才能使用的仓库

远程仓库分为三种，中央仓库、中央仓库的镜像、私服

中央仓库：最权威的，所有的开发人员都共享使用的一个集中的仓库`https://repo.maven,apache.org`中央仓库地址

中央仓库的镜像：就是中央仓库的备份，在各大洲，重要的城市都有镜像

私服：在公司内部，在局域网中使用，不是对外使用的

### 2.2.3 仓库的使用

maven仓库的使用不需要人为参与

开发人员需要使用mysql驱动 ==> maven首先查看本地仓库 ==> 私服 ==> 镜像 ==> 中央仓库

在 Maven 构建项目的过程中如果需要某些插件，首先会到 Maven 的本地仓库中查找，如果找到则可以直接使用；如果找不到，它会自动连接外网，到远程中央仓库中查找；如果远程仓库中能找到，则先把所需要的插件下载到本地仓库，然后再使用，并且下次再用到相同的插件也可以直接使用本地仓库的；如果没有外网或者远程仓库中也找不到，则构建失败。  

## 2.3 POM

POM( Project Object Model ) 即项目对象模型。 Maven 把一个项目的结构和内容抽象成一个模型，在 xml 文件中进行声明，以方便进行构建和描述， pom.xml 是 Maven 的灵魂。所以， maven 环境搭建好之后，所有的学习和操作都是关于 pom.xml 的  

|    标签名    |                             描述                             |
| :----------: | :----------------------------------------------------------: |
| modelVersion |               Maven模型的版本，目前只能是4.0.0               |
|   groupId    | 组织 id，一般是公司域名的倒写。 格式可以为： 1. 域名倒写。 例如 com.baidu 2. 域名倒写+项目名。例如 com.baidu.appolo |
|  artifactId  |    项目的名称,也是模块名称，对应的groupID中项目中的子项目    |
|   version    | 项目的版本号。如果项目还在开发中，是不稳定版本， 通常在版本后带-SNAPSHOT version 使用三位数字标识，例如 1.1.0 |
|  packaging   | 项目打包的类型，可以使 jar、 war、 rar、 ear、 pom，默认是 jar |

## 2.4 坐标

> :notes:
>
> 坐标：唯一值， 在互联网中唯一标识一个项目的
> \<groupId>公司域名的倒写\</groupId>
>
> \<artifactId>自定义项目名称\</artifactId>
>
> \<version>自定版本号\</version>

 https://mvnrepository.com/ 搜索使用的中央仓库， 通过这个网址可以搜索到使用的各种资源，使用groupId 或者 artifactId作为搜索条件

2） packaging： 打包后压缩文件的扩展名，默认是jar ，web应用是war 
	      packaging 可以不写， 默认是jar

## 2.5 依赖

dependencies 和dependency ，相当于是 java代码中import



```xml
<dependencies>
	<!--依赖  java代码中 import -->
	<dependency>
		<groupId>mysql</groupId>
		<artifactId>mysql-connector-java</artifactId>
		<version>5.1.9</version>
		</dependency>
</dependencies>
```

​	  4）properties：设置属性

​	  5）build ： maven在进行项目的构建时， 配置信息，例如指定编译java代码使用的jdk的版本等



| 继承   |                                                              |
| ------ | ------------------------------------------------------------ |
| parent | 在 Maven 中，如果多个模块都需要声明相同的配置，例如： groupId、 version、 有相同的依赖、或者相同的组件配置等， 也有类似 Java 的继承机 制， 用 parent 声明要继承的父工程的 pom 配置。 |







| 聚合    |                                                              |
| ------- | ------------------------------------------------------------ |
| modules | 在 Maven 的多模块开发中，为了统一构建整个项目的所有模块，可以提供一 个额外的模块，该模块打包方式为 pom，并且在其中使用 modules 聚合的 其它模块，这样通过本模块就可以一键自动识别模块间的依赖关系来构建所有 模块，叫 Maven 的聚合。 |



## 2.6 生命周期(了解)

Maven的生命周期：就是Maven构建项目的过程，清理，编译，测试、报告、打包、安装、部署

maven的命令：maven独立使用，通过命令，完成maven的生命周期的执行。
              maven可以使用命令，完成项目的清理，编译，测试等等

maven的插件： maven命令执行时，真正完成功能的是插件，插件就是一些jar文件， 一些类。

### 使用步骤

​     1.加入依赖，在pom.xml加入单元测试依赖

```xml
<!--单元测试-->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

2. 在maven项目中的src/test/java目录下，创建测试程序。
       推荐的创建类和方法的提示：
   	 1.测试类的名称 是Test + 你要测试的类名
   	 2.测试的方法名称 是：Test + 方法名称

   	 例如你要测试HelloMaven ,
   	 创建测试类 TestHelloMaven

      @Test
   	 public void testAdd(){
         测试HelloMaven的add方法是否正确
   	 }
   其中testAdd叫做测试方法，它的定于规则

3. 方法是public，必须的

4. 方法没有返回值，必须的

5. 方法名称是自定义的，推荐是Test+方法名称

6. 在方法的上面加入@Test



## 2.7 Maven的常用命令

Maven 对所有的功能都提供相对应的命令，要想知道 maven 都有哪些命令，那要看 maven 有哪些功能。
maven 三大功能： 管理依赖、构建项目、管理项目信息。 

管理依赖，只需要声明就可以自动到仓库下载；

管理项目信息其实就是生成一个站点文档，一个命令就可以解决；

那 maven 功能的是项目构建。
Maven 提供一个项目构建的模型，把编译、测试、打包、部署等都对应成一个个的生命周期阶段， 并对每一个阶段提供相应的命令，程序员只需要掌握一小堆命令，就可以完成项目的构建过程。
mvn clean 清理(会删除原来编译和测试的目录，即 target 目录，但是已经 install 到仓库里的包不会删除)

| mvn compile      | 编译主程序(会在当前目录下生成一个 target,里边存放编译主程序之后生成的字节码文件) |
| ---------------- | ------------------------------------------------------------ |
| mvn test-compile | 编译测试程序(会在当前目录下生成一个 target,里边存放编译测试程序之后生成的字节码文件) |
| mvn test         | 测试(会生成一个目录surefire-reports，保存测试结果)           |

mvn package：打包主程序(会编译、编译测试、测试、并且按照 pom.xml 配置把主程序打包生成 jar 包或者 war 包)，只会将`src/main`下的文件打包，不打包测试目录下的文件
mvn install 安装主程序(会把本工程打包，并且按照本工程的坐标保存到本地仓库中)
mvn deploy 部署主程序(会把本工程打包，按照本工程的坐标保存到本地库中，并且还会保存到私服仓库中。还会自动把项目部署到 web 容器中)。一般不使用
注意：执行以上命令必须在命令行进入 pom.xml 所在目录！

执行某个阶段的时候，前面的所有阶段都会执行

	mvn compile 
	   编译main/java/目录下的java 为class文件， 同时把class拷贝到 target/classes目录下面
		把main/resources目录下的所有文件 都拷贝到target/classes目录下

## 2.8 核心插件(了解)



插件可以在自己的项目中设置， 最常使用的是 maven 编译插件。设置项目使用的 jdk 版本时通过编译插件指定。
pom.xml 文件\<build>中设置。  

```xml
控制配置maven构建项目的参数设置，设置jdk的版本
<build>
    <!--配置插件-->
	<plugins>
        配置具体的插件
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
            插件的名称
			<artifactId>maven-compiler-plugin</artifactId>
            插件的版本
			<version>3.8.1</version>
            配置插件的信息
			<configuration>
                告诉maven，写的代码是在jdk1.8上编译的
				<source>1.8</source>
                程序应该是运行在1.8jdk上
				<target>1.8</target>
			</configuration>
		</plugin>
	</plugins>
</build>
```



# 第三章:Maven在IDEA中的应用



## 3.1 idea和maven结合

idea中内置了maven ，一般不使用内置的， 因为用内置修改maven的设置不方便。

使用自己安装的maven， 需要覆盖idea中的默认的设置。让idea指定maven安装位置等信息

配置的入口 ①：配置当前工程的设置， file--settings ---Build, Excution,Deployment--Build Tools

                   --Maven 
    					   Maven Home directory: maven的安装目录
    						User Settings File :  就是maven安装目录conf/setting.xml配置文件
    						Local Repository :    本机仓库的目录位置
    
    				   --Build Tools--Maven--Runner  
    					  VM Options : archetypeCatalog=internal
    					  JRE: 你项目的jdk


                    archetypeCatalog=internal , maven项目创建时，会联网下载模版文件，
    					  比较大， 使用-DarchetypeCatalog=internal，不用下载， 创建maven项目速度快。



	            ②：配置以后新建工程的设置， file--other settings--Settings for New Project



# 第四章: 依赖管理

依赖范围,使用scope表示

scope的值有compile，test，provided，默认是compile

scope：表示依赖使用的范围，也就是在maven构建项目的那些阶段中起作用

maven构建项目 编译，测试，打包，安装，部署，过程（阶段）



依赖的范围： compile、 test、 provided，默认采用 compile

|    项目构建过程    | compile | test | provided |
| :----------------: | :-----: | :--: | :------: |
|  对主程序是否有效  |   是    |  否  |    是    |
| 对测试程序是否有效 |   是    |  是  |    是    |
|    是否参与打包    |   是    |  否  |    否    |
|    是否参与部署    |   是    |  否  |    否    |

```xml
   junit的依赖范围是 test
	<dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>

	<dependency>
      <groupId>a</groupId>
      <artifactId>b</artifactId>   b.jar
      <version>4.11</version>
    </dependency>


	 <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>          servlet.jar
      <scope>provided</scope> 提供者
    </dependency>
```



  你在写项目的中的用到的所有依赖（jar ） ，必须在本地仓库中有。
	没有必须通过maven下载， 包括provided的都必须下载。

	你在servlet需要继承HttpServlet( provided) , 你使用的HttpServlet是maven仓库中的。
	
	当你的写好的程序， 放到 tomat服务器中运行时， 此时你的程序中不包含servlet的jar
	因为tomcat提供了 servlet的.jar

> :question:如何确定使用哪个版本的jar包？
>
> - 查看官网的版本进行功能版本适配,或者项目指定的版本

# 第五章：Maven常用设置

## 5.1 Maven的属性设置

`<properties>`设置maven的常用设置

## 5.2 全局变量

1. 自定义的属性,在`<properties>`通过自定义标签声明变量(标签名就是变量名)

2. 在pom.xml文件中的其他位置，使用${标签名}使用变量的值

自定义全局变量一般是定义 依赖的版本号，当你的项目中要使用多个相同的版本号，先使用全局变量定义，再使用${变量名}



```xml
定义全局变量
<properties>
	<spring.version>4.3.10.RELEASE</spring.version>
</properties>
引用全局变量
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>${spring.version}</version>
</dependency>
```



```xml
<properties>
<maven.compiler.source>1.8</maven.compiler.source> 源码编译 jdk 版本
<maven.compiler.target>1.8</maven.compiler.target> 运行代码的 jdk 版本
<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> 项目构建使用的编码，避免中文乱
码
<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding> 生成报告的编码
</properties>
```



## 5.3 资源插件

src/main/java 和 src/test/java 这两个目录中的所有*.java 文件会分别在 comile 和 test-comiple 阶段被编译，编译结果分别放到了 target/classes 和 targe/test-classes 目录中，但是这两个目录中的其他文件都会被忽略掉，如果需要把 src 目录下的文件包放到 target/classes 目录，作为输出的 jar 一部分。需要指定资源文件位置。 以下内容放到\<buid>标签中  

```xml
<build>
	<resources>
		<resource>
			<directory>src/main/java</directory><!--所在的目录-->	
				<includes><!--包括目录下的.properties,.xml 文件都会扫描到-->
					<include>**/*.properties</include>
					<include>**/*.xml</include>
				</includes>
				<!--filtering 选项 false 不启用过滤器， *.property 已经起到过滤的作用了 -->
				<filtering>false</filtering>
		</resource>
	</resources>
</build>
```

1. 默认没有使用resources的时候， maven执行编译代码时， 会把src/main/resource目录中的文件拷贝到target/classes目录中。

对于src/main/java目录下的非java文件不处理，不拷贝到target/classes目录中

2. 我们的程序有需要把一些文件放在src/main/java目录中，当我再执行java程序时，需要用到src/main/java目录中的文件。需要告诉maven再maven compile src/main/java目录下的程序时，需要把文件一同拷贝到target/classes目录中。此时就需要再\<build>中加入\<resources>
# Maven 管理多模块应用

## 为什么需要多模块

:persevere:随着项目的不断发展，需求的不断细化与添加，代码越来越多，结构也越来越复杂，这时候就会遇到各种问题

- 不同方面的代码之间相互耦合，这时候一系统出现问题很难定位到问题的出现原因，即使定位到问题也很难修正问题，可能在修正问题的时候引入更多的问题。
- 多方面的代码集中在一个整体结构中，新入的开发者很难对整体项目有直观的感受，增加了新手介入开发的成本，需要有一个熟悉整个项目的开发者维护整个项目的结构（通常在项目较大且开发时间较长时这是很难做到的）
- 开发者对自己或者他人负责的代码边界很模糊，这是复杂项目中最容易遇到的，导致的结果就是开发者很容易修改了他人负责的代码且代码负责人还不知道，责任追踪很麻烦

:+1:将一个复杂项目拆分成多个模块是解决上述问题的一个重要方法。 **拆分的好处**

- 多模块的划分可以降低代码之间的耦合性（从类级别的耦合提升到jar包级别的耦合）
- 每个模块都可以是自解释的（通过模块名或者模块文档）
- 模块还规范了代码边界的划分，开发者很容易通过模块确定自己所负责的内容

## 模块拆分策略

:older_man:推荐按照功能拆分、方便后期转换为微服务架构

### 按职责划分

- --module-test
  - --module-test-service
  - --module-test-po
  - --module-test-controller
  - --module-test-dao
  - --module-test-common
  - --module-test-util

### 按照功能拆分

在电商系统中如下module

- --module-test
  - --module-test-common公共部分
  - --module-test-order订单
  - --module-test-checkout购物车
  - --module-test-pay支付
  - --module-test-catory类目
  - --module-test-product商品
  - --module-test-price价格
  - --module-test-account账号




packaging 标签指的是打包的方式

默认为`jar`，如果pom文件中没有packaging标签那么默认就是jar

pom是项目对象模型（Project Object Module），该文件是可以被



maven父工程



# 构建多模块应用

搭建多模块项目，需要使用 maven 的继承和聚合

:sparkles:Maven中聚合和继承的特点

- 聚合负责将多个模块集中在一起进行管理

- 继承则负责各子模块中的公共配置

## 创建项目

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210902215119534.png)

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210902215636541.png)

## 修改父工程

- 删除src目录

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210902215741035.png)

## 创建子模块

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210902215915860.png)

![image-20210902215119534](img/image-20210902215119534.png)

<h1 align="center">注意变化</h1>

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210902220036026.png)

子模块创建完成如下

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210902220412189.png)

## 父工程pom

此时父工程的`pom.xml`文件发生了一些变化

```xml
<groupId>org.example</groupId>
<artifactId>crowfunding</artifactId>
<packaging>pom</packaging>
<version>1.0-SNAPSHOT</version>
<modules>
    <module>crowfundint-01-admin-parent</module>
</modules>
```

:sparkles:更改打包方式为`pom`的原因

- pom是最简单的打包类型，不像jar和war，它生成的构件只有它本身，将`packaging`设置为pom,意味着没有代码需要测试或者编译，也没有资源需要处理
- 由于使用了聚合，所以打包方式必须为pom，否则无法构建

```xml
<modules>
    <module>crowfundint-01-admin-parent</module>
</modules>
```

:notes:module的值是子模块相对于当前 POM 的路径

## 子模块pom

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210902230507544.png)

```xml
    <parent>
        <artifactId>crowdfunding</artifactId>
        <groupId>com.pineappleman</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>crowdfunding-01-admin-parent</artifactId>
```

:notes:声明了该模块继承自`com.pineappleman:crowdfunding:1.0-SNAPSHOT`,其实这里面还省略了 `<relativePath></relativePath>` 由于 **relativePath** 默认是 `../pom.xml` 而我们的子项目确实在父项目的下一级目录中，所以是可以不用填写的

> :sparkles:Maven首先在当前构建项目的环境中查找父pom，然后项目所在的文件系统查找，然后是本地存储库，最后是远程repo

**artifactId** 是子模块的组件id，由于继承了父pom，所以**groupId**、**version** 也可以不写，不写的话就默认继承自父pom



# 设置父工程编译级别

:sparkles:项目中会统一使用JDK版本和编译级别，所以项目的编译级别必须统一一致，那么将编译插件添加到父工程，子模块依然会无条件去继承父工程的插件

## 修改父工程pom文件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.8.1</version>
            <!--编译级别-->
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <!--编码格式-->
                <encoding>UTF-8</encoding>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## 查看指定编译级别后

在 `File -> Settings -> Build, Execution, Deployment -> Compiler -> Java Compiler `查看

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210902231341998.png)

# Maven 依赖版本管理

## 父工程添加依赖

:book:在父工程`<dependencies>`标签中添加三方依赖，子模块会无条件继承父工程所有依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.2.8</version>
    </dependency>

</dependencies>
```

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210902233256349.png)

## 父工程管理依赖版本号

通过以上依赖方式，子模块会无条件继承父工程的所有依赖，导致的问题是，不需要的继承的依赖也会被继承，最终将<font style="color:red;font-weight:bold">增加项目最终打包的大小，也可能为上线埋下隐患</font>

:sparkles:父工程管理的是所有项目模块的依赖，而不是某一个项目模块的依赖，所以某一个项目模块不需要继承父工程中的所有依赖，这就需要子项目模块向父工程声明需要的依赖即可（**声明式依赖**），此时父工程只需要管理依赖的版本号即可

### 父工程添加 dependencyManagement 标签管理依赖

```xml
<dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.2.8</version>
            </dependency>
    </dependencies>
</dependencyManagement>
```

:notes:子模块项目之前继承的依赖消失，由于父工程通过 dependencyManagement 标签管理依赖，那么之前子模块无条件继承的依赖就全部消失

:notes:用来申明依赖，但不导入，只有子模块真正需要使用的时候才导入依赖

:sparkles:`dependencyManagement `特点

- 子项目不会继承 dependencyManagement 组件中声明的依赖
- 但如果子项目想导入某个父pom 中 dependencyManagement 中的依赖，只需要填写 **groupId 和 artifactId** ,不需要填写版本号，maven会自动去父pom 的 dependencyManagement 中找对应的 version，包括scope、exclusions等

### 父工程添加 properties 管理版本号

在 properties 标签中，可以自定义标签名称来管理依赖的版本号

:notes:通常自定义的标签名称由`项目名称-version 构成`，被管理的依赖版本号由**${给定标签名称}**来代替

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210903000526903.png)

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210903000531850.png)

:notes:依赖版本的查找

- Maven会沿着父子层次向上走，直到找到一个拥有 dependencyManagement 组件的项目，然后在其中查找，如果找到则返回申明的依赖，没有继续往下找

### 子模块声明式添加依赖

由于父工程管理依赖的版本号，那么子模块要想继承依赖，只能通过声明式来添加依赖， 实际上，子模块中的依赖是继承父工程依赖的版本号；如果子模块已定义依赖版本号，那么以子模块定义的版本号为准

![](https://gitee.com/mingchaohu/blog-image/raw/master/image/image-20210903000601237.png)

## `dependencies`与`dependencyManagement`的区别

:sparkles:`dependencies`

- 引入依赖
- 即时子项目不写`dependencies`，子项目仍然会从父项目中继承`dependencies`中的所有依赖项

:sparkles:`dependencyManagement`

- 声明依赖，并不引入依赖
- 子项目默认不会继承父项目`dependencyManagement`中的依赖
- 只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承（version，exclusions，scope等读取自父pom）
- 子项目如果指定了依赖的具体版本号，会优先使用子项目中指定版本，不会继承父pom中申明的依赖

# 附录

[Maven多模块管理](https://juejin.cn/post/6844903970024980488)
[maven 依赖版本管理](https://juejin.cn/post/6844903965444816903)

[Maven与gradle的对比](https://zhuanlan.zhihu.com/p/21394120)

https://blog.csdn.net/Ginny_2017/article/details/105429394

