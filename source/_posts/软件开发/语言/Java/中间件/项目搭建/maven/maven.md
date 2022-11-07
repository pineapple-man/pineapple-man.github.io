---
title: Maven 工具的使用
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Java
tags: 项目
keywords: Maven
excerpt: 其实一个复杂的 Java 项目，总是需要很多其他人已经完成的组件（避免重复造轮子），在前端中可以使用 npm 或 yarn 等包管理工具添加依赖，由于我主要使用 maven 工具，所以主要介绍 maven 工具下的开发流程
date: 2022-01-08 20:02:45
thumbnailImage:
---

<!-- toc -->

## Maven 简介

明白`Maven`的重要性，需要先从<font style="color:red;font-weight:bold">软件工程</font>这件事说起

### 软件工程

软件工程，顾名思义就是软件的工程，要了解这个词就需要分别了解两个词「软件」和「工程」，其中软件顾名思义就是使用计算机编程语言所生成的应用程序

:question:什么是工程？
{% alert success no-icon %}
各行业的从业人员通过总结规律或者方法，以最短的时间和人力、物力来做出高效可靠的东西；且这种规律或方法是<font style="color:red;font-weight:bold">可复用</font>的
{% endalert %}  
:sparkles:什么是软件工程?
{% alert success no-icon %}
为了能够实现<font style="color:red;font-weight:bold">软件的流水线生产</font>，软件工程是**设计和构建软件**时的<font style="color:red;font-weight:bold">一种规范和工程化的方法</font>。
{% endalert %}

### 软件工程的工作流程

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/软件工程的工作流程.png %}

上述过程需要**重复多次迭代**，在大型工程中<font style="color:red;font-weight:bold">构建项目是比较复杂的</font>，传统开发项目(没有使用自动化管理工具)存在的问题
{% alert success no-icon %}

1. 项目**模块繁杂**，管理不便
2. **第三方依赖众多**，资源查询不便
3. 控制各个 jar 包的**版本**，管理不便
4. 查询 jar 包的依赖，管理不便
   {% endalert %}
   {% alert info no-icon %}
   <font style="color:red;font-weight:bold">Maven 这个自动化构建工具</font>，可以让我们从上面的工作中解脱出来
   {% endalert %}

### Maven 概述

:thinking:什么是 Maven?​
{% alert success no-icon %}
Maven 是 Apache 软件基金会组织维护的一款**自动化构建工具**,专注服务于<font style="color:red;font-weight:bold"> Java 平台的项目构建和依赖管理</font>
{% endalert %}

:grey_question:Maven 能干什么？
{% alert success no-icon %}

1. Maven 可以管理 jar 文件
2. 自动下载 jar 和它的文档，源代码
3. 管理 jar 包的依赖
4. 管理需要的 jar 的版本
5. 帮助编译程序，将 java 编译为 class
6. 帮助测试代码是否正确
7. 帮助打包文件，形成 jar 文件，或者 war 文件
8. 帮助部署项目
   {% endalert %}

构建：是面向过程的(从开始到结尾的多个步骤)，涉及到多个环节的协同工作，构建过程中各个环节执行动作如下：
{% alert success no-icon %}

1. 清理：删除以前的编译结果，为重新编译做好准备
2. 编译：将 Java 源程序编译为字节码文件
3. 测试：针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性
4. 报告：在每一次测试后以标准的格式记录和展示测试结果
5. 打包：将一个包含诸多文件的工程封装为一个压缩文件用于安装或部署。Java 工程对应 jar 包，Web 工程对应 war 包
6. 安装：在 Maven 环境下特指将打包的结果——jar 包或 war 包安装到本地仓库中
7. 部署：将打包的结果部署到远程仓库或将 war 包部署到服务器上运行
   {% endalert %}

### Maven 核心概念

Maven 能够实现自动化构建和它的内部原理分不开，Maven 共有九个核心概念
{% alert success no-icon %}

1. POM：POM 是一个文件，文件名称为`pom.xml`，pom 翻译过来叫做**项目对象模型**。maven 把一个项目当作一个模型使用，控制 maven 构建项目的过程，管理 jar 依赖
2. 约定的目录结构：Maven 项目的目录和文件的位置都是规定的
3. 坐标：是一个唯一的字符串，用来表示资源的
4. 依赖管理：管理你的项目可以使用的 jar 文件
5. 仓库管理：资源存放的位置
6. 生命周期：maven 工具构建项目的过程，就是生命周期
7. 插件和目标：执行 maven 构建的时候用的工具是插件
8. 继承：用于多模块管理
9. 聚合：用于多模块管理

{% endalert %}

## 安装 Maven

Mavan 也是一种软件，因此本小节主要记录如何安装 Maven

### Windows 下安装

:notes:Maven 安装步骤
{% alert warning no-icon %}

1. 从 maven 的官网下载 maven 的安装包`apache-maven-3.3.9-bin.zip`
2. 解压安装包，<font style="color:red;font-weight:bold">解压到非中文目录</font>
3. 配置环境变量（指定 maven 工具安装目录/bin）
4. 验证是否安装成功,命令行中执行`mvn -v`:notes:(需要配置 Java_HOME)，用来指定 JDK 路径

{% endalert %}
:sparkles:Maven 软件目录结构

1. `bin` ：存放许多执行程序(如：mvn.cmd)
2. `conf` ：maven 工具本身的配置文件 settings.xml

:notes:建议使用 3.3.9 版本，直接适应用 JDK1.8,避免使用最新版 Maven 出现不知道的错误

### Linux 下安装

目前 Maven 主要维护的版本有：

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210320123556811.png %}
:point_right:[点击查看 Maven 的版本信息](https://maven.apache.org/docs/history.html)

:notes:选择 3.3.9 版本使用官方的源码包进行安装，主要的步骤如下：
:one: 下载并解压
{% alert warning no-icon %}

1. 通过官网下载相应的`apache-maven-3.3.9-bin.tar.gz`压缩包
2. 下载完成之后并解压对应的文件，`tar zxcf apache-maven-3.3.9-bin.tar.gz`

{% endalert %}

:two: 配置
{% alert warning no-icon %}

1. 编辑 `/etc/profile`，配置对应的环境变量

```shell
// 安装目录
export MAVEN_HOME=/home/homer/Apache-maven/apache-maven-3.3.9
export PATH=${MAVEN_HOME}/bin:${PATH}
```

2. 重新载入`source /etc/profile`

{% endalert %}

:three: 验证
{% alert warning no-icon %}

输入`mvn -v`查看是否显示版本信息

{% endalert %}

:point_right:如果想要更详细的步骤，[点击查看官方安装步骤](https://maven.apache.org/install.html)

### 配置阿里云环境

国内使用，总是不能友好的访问外网的资源，所以需要配置阿里云地址，具体的方法就是修改`conf/settings.xml`文件中的`mirrors`内容为如下部分

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

:notes:`settings.xml`文件需要格式化, [xml 文件格式化网站](https://c.runoob.com/front-end/710)

## Maven 的核心概念

### Maven 工程目录结构

每一个 maven 项目在磁盘中都是一个文件夹，通过项目构建工具生成的项目总是具有相应的目录结构，Maven 生成的目录结构及对应的含义如下：

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

项目创建完成,使用`mvn compile`编译 src/main 目录下的所有 java 文件,检查 maven 是否已经管理项目，如果成功，会下载很多插件,并在项目的根目录下生成`target`目录,Maven 编译的 java 程序,所有的 class 文件都放在 target 目录中

:question:如何设置本机存放资源的目录位置(设置本机仓库):
{% alert warning no-icon %}

1. 修改 maven 的配置文件， maven 安装目录/conf/settings.xml
   先备份 settings.xml
2. 修改 \<localRepository> 指定你的目录（不要使用中文目录）

{% endalert %}

### 仓库

maven 是如何做到自动帮助用户托管项目，项目依赖的 jar 包从哪儿获取呢？
{% alert success no-icon %}

这一切都是由于存在 maven 仓库的概念,Maven 核心程序仅仅定义了自动化构建项目的生命周期，但具体的构建工作是由特定的构件完成的。而且为了提高构建的效率和构件复用， maven 把所有的构件统一存储在某一个位置，这个位置就叫做**仓库**
{% endalert %}

:notes:仓库是存放 Maven 及项目正常运行所需的 Jar 包的位置,主要存放的有:
{% alert success no-icon %}

- maven 使用的插件(各种 jar)
- 项目使用的 jar（第三方的工具）

{% endalert %}

仓库共分为两种：本地仓库和远程仓库

| 仓库的分类 |                 作用                 |
| :--------: | :----------------------------------: |
|  本地仓库  | 本地计算机上的文件夹,存放各种 jar 包 |
|  远程仓库  |  在互联网上，使用网络才能使用的仓库  |

远程仓库分为三种，中央仓库、中央仓库的镜像、私服
{% alert warning no-icon %}

- 中央仓库：最权威的，所有的开发人员都共享使用的一个集中的仓库`https://repo.maven,apache.org`中央仓库地址
- 中央仓库的镜像：就是中央仓库的备份，在各大洲，重要的城市都有镜像
- 私服：在公司内部，在局域网中使用，不是对外使用的

{% endalert %}

maven 仓库的使用不需要人为参与，例如：开发人员需要使用 mysql 驱动，maven 会自动去仓库中查找/下载，maven 的查找顺序是：
{% alert warning no-icon %}

查看本地仓库 ==> 私服 ==> 镜像 ==> 中央仓库

{% endalert %}

在 Maven 构建项目的过程中如果需要某些插件，首先会到 Maven 的本地仓库中查找，如果找到则可以直接使用；如果找不到，它会自动连接外网，到远程中央仓库中查找；如果远程仓库中能找到，则先把所需要的插件下载到本地仓库，然后再使用，并且下次再用到相同的插件也可以直接使用本地仓库的；如果没有外网或者远程仓库中也找不到，则构建失败。

### POM

POM( Project Object Model ) 即项目对象模型。 Maven 把一个项目的结构和内容抽象成一个模型，在 xml 文件中进行声明，以方便进行构建和描述， pom.xml 是 Maven 的灵魂。所以， maven 环境搭建好之后，所有的学习和操作都是关于 pom.xml 的

|    标签名    |                                                        描述                                                         |
| :----------: | :-----------------------------------------------------------------------------------------------------------------: |
| modelVersion |                                         Maven 模型的版本，目前只能是 4.0.0                                          |
|   groupId    | 组织 id，一般是公司域名的倒写。 格式可以为： 1. 域名倒写。 例如 com.baidu 2. 域名倒写+项目名。例如 com.baidu.appolo |
|  artifactId  |                              项目的名称,也是模块名称，对应的 groupID 中项目中的子项目                               |
|   version    |    项目的版本号。如果项目还在开发中，是不稳定版本， 通常在版本后带-SNAPSHOT version 使用三位数字标识，例如 1.1.0    |
|  packaging   |                           项目打包的类型，可以使 jar、 war、 rar、 ear、 pom，默认是 jar                            |

### 坐标

{% alert success no-icon %}

:notes: 坐标是一个唯一值， 在互联网中唯一标识一个项目的，具体的坐标也称之为 GAV 坐标，形式如下：

```xml
<groupId>公司域名的倒写</groupId>
<artifactId>自定义项目名称</artifactId>
<version>自定版本号</version>
```

{% endalert %}

### 依赖

dependencies 和 dependency ，相当于是 java 代码中 import

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

properties：设置属性

build ： maven 在进行项目的构建时， 配置信息，例如指定编译 java 代码使用的 jdk 的版本等

### 继承

{% alert success no-icon %}

在 Maven 中，如果多个模块都需要声明相同的配置，例如： groupId、 version、 有相同的依赖、或者相同的组件配置等， 也有类似 Java 的继承机 制， 用 `parent` 声明要继承的父工程的 pom 配置。

{% endalert %}

### 聚合

{% alert success no-icon %}

在 Maven 的多模块开发中，为了统一构建整个项目的所有模块，可以提供一个额外的模块，该模块打包方式为 pom，并且在其中使用 modules 聚合的其它模块，这样通过本模块就可以一键自动识别模块间的依赖关系来构建所有模块，叫 Maven 的聚合。

{% endalert %}

### 生命周期

{% alert success no-icon %}

Maven 的生命周期，就是 Maven 构建项目的过程，总共有清理，编译，测试、报告、打包、安装、部署过程这些过程

{% endalert %}

:notes:maven 的插件： maven 命令执行时，真正完成功能的是插件，插件就是一些 jar 文件， 一些类

## Maven 常用命令

{% alert success no-icon %}

Maven 对所有的功能都提供相对应的命令，其中 maven 具有三大核心功能： **管理依赖**、**构建项目**、**管理项目信息**。管理依赖，只需要声明就可以自动到仓库下载；管理项目信息其实就是生成一个站点文档，一个命令就可以解决；因此 Maven 的常用命令和项目构建相关

{% endalert %}

Maven 提供一个项目构建的模型，把编译、测试、打包、部署等都对应成一个个的生命周期阶段，并对每一个阶段提供相应的命令，程序员只需要掌握一些命令，就可以完成项目的构建过程

|    maven 命令    |                                                               含义                                                                |
| :--------------: | :-------------------------------------------------------------------------------------------------------------------------------: |
|   mvn compile    |                         编译主程序(会在当前目录下生成一个 target,里边存放编译主程序之后生成的字节码文件)                          |
|    mvn clean     |                      清理(会删除原来编译和测试的目录，即 target 目录，但是已经 install 到仓库里的包不会删除)                      |
| mvn test-compile |                       编译测试程序(会在当前目录下生成一个 target,里边存放编译测试程序之后生成的字节码文件)                        |
|     mvn test     |                                        测试(会生成一个目录 surefire-reports，保存测试结果)                                        |
|   mvn package    |                     按照 pom.xml 配置把`src/main`下的文件打包,生成 jar 包或者 war 包，不打包测试目录下的文件                      |
|   mvn install    |                                 安装主程序(会把本工程打包，并且按照本工程的坐标保存到本地仓库中)                                  |
|    mvn deploy    | 部署主程序(会把本工程打包，按照本工程的坐标保存到本地库中，并且还会保存到私服仓库中。还会自动把项目部署到 web 容器中)。一般不使用 |

{% alert info no-icon %}

:notes:执行以上命令必须在命令行进入 pom.xml 所在目录！,执行某个阶段的时候，前面的所有阶段都会执行

{% endalert %}

mvn compile 详解
{% alert success no-icon %}

编译 main/java/ 目录下的 java 为 class 文件， 同时把 class 拷贝到 target/classes 目录下面，把 main/resources 目录下的所有文件 都拷贝到 target/classes 目录下

{% endalert %}

## Maven 常用设置

了解了常用命令以及一些基础概念之后，某些项目可能有更详细的配置，接下来介绍 Maven 常用的设置

### scope 设置

scope 表示依赖使用的范围，也就是在 maven 构建项目的那些阶段中起作用，scope 的值有 compile，test，provided，默认是 `compile`，不同 scope 对项目的影响如下：

|    项目构建过程    | compile | test | provided |
| :----------------: | :-----: | :--: | :------: |
|  对主程序是否有效  |   是    |  否  |    是    |
| 对测试程序是否有效 |   是    |  是  |    是    |
|    是否参与打包    |   是    |  否  |    否    |
|    是否参与部署    |   是    |  否  |    否    |

具体配置的实例如下：
{% alert warning no-icon %}

```xml
   junit的依赖范围是 test
	<dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>

	 <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>
```

{% endalert %}

### Maven 属性设置

`<properties>`声明项目中的变量，具体使用方式如下：
{% alert success no-icon %}

1. 自定义的属性,在`<properties>`通过自定义标签声明变量(标签名就是变量名)，一般是定义依赖的版本号
2. 在 `pom.xml` 文件中的其他位置，使用 `${标签名}` 使用变量的值

{% endalert %}
变量声明的配置如下：
{% alert warning no-icon %}

```xml
<!--定义全局变量-->
<properties>
	<spring.version>4.3.10.RELEASE</spring.version>
</properties>
<!--引用全局变量-->
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>${spring.version}</version>
</dependency>
```

{% endalert %}

### 资源插件设置

{% alert success no-icon %}

`src/main/java` 和 `src/test/java` 这两个目录中的所有 `*.java` 文件会分别在 comile 和 test-comiple 阶段被编译，编译结果分别放到了 `target/classes` 和 `targe/test-classes` 目录中，但是这两个目录中的其他文件都会被忽略掉，如果需要把 src 目录下的文件包放到 `target/classes` 目录，作为输出的 jar 一部分。需要指定资源文件位置，相关配置内容放到`<buid>`标签中

{% endalert %}
资源插件的配置如下：
{% alert warning no-icon %}

```xml
<build>
	<resources>
		<resource>
            <!--所在的目录-->
			<directory>src/main/java</directory>
				<includes>
                <!--包括目录下的.properties,.xml 文件都会扫描到-->
					<include>**/*.properties</include>
					<include>**/*.xml</include>
				</includes>
<!--filtering 选项 false 不启用过滤器， *.property 已经起到过滤的作用了 -->
				<filtering>false</filtering>
		</resource>
	</resources>
</build>
```

{% endalert %}

:notes:常见的问题就是 MyBatis 的 Mapper 文件的配置

## Maven 在 IDEA 中的应用

idea 中内置了 maven ，一般不使用内置的，因为用内置修改 maven 的设置不方便，使用自己安装的 maven，需要覆盖 idea 中的默认的设置。让 idea 指定 maven 安装位置等信息

:one: 配置当前工程的设置， `file--settings ---Build, Excution,Deployment--Build Tools`

```
--Maven
    Maven Home directory: maven的安装目录
    	User Settings File :  就是maven安装目录conf/setting.xml配置文件
    	Local Repository :    本机仓库的目录位置
--Build Tools--Maven--Runner
    VM Options : archetypeCatalog=internal
    JRE: 你项目的jdk

archetypeCatalog=internal , maven项目创建时，会联网下载模版文件，比较大，
        使用DarchetypeCatalog=internal，不用下载， 创建maven项目速度快
```

:two: 配置以后为新建工程的设置， file--other settings--Settings for New Project

## Maven 管理多模块应用

多模块应用是大型项目必备的一种状态，本小节主要记录如何使用 Maven 进行多模块应用的管理

### 为什么需要多模块

:persevere:随着项目的不断发展，需求的不断细化与添加，代码越来越多，结构也越来越复杂，这时候就会遇到如下各种问题：
{% alert success no-icon %}

- 不同方面的代码之间相互耦合，这时候一系统出现问题很难定位到问题的出现原因，即使定位到问题也很难修正问题，可能在修正问题的时候引入更多的问题。
- 多方面的代码集中在一个整体结构中，新入的开发者很难对整体项目有直观的感受，增加了新手介入开发的成本，需要有一个熟悉整个项目的开发者维护整个项目的结构（通常在项目较大且开发时间较长时这是很难做到的）
- 开发者对自己或者他人负责的代码边界很模糊，这是复杂项目中最容易遇到的，导致的结果就是开发者很容易修改了他人负责的代码且代码负责人还不知道，责任追踪很麻烦

{% endalert %}

:+1:将一个复杂项目拆分成多个模块是解决上述问题的一个重要方法，通过将应用模块化具有如下好处：
{% alert success no-icon %}

- 多模块的划分可以降低代码之间的耦合性（从类级别的耦合提升到 jar 包级别的耦合）
- 每个模块都可以是自解释的（通过模块名或者模块文档）
- 模块还规范了代码边界的划分，开发者很容易通过模块确定自己所负责的内容

{% endalert %}

### 模块拆分策略

{% alert info no-icon %}

推荐按照**功能拆分**、方便后期转换为微服务架构

{% endalert %}

#### 按职责划分

```
-- module-test
 --module-test-service
 --module-test-po
 --module-test-controller
 --module-test-dao
 --module-test-common
 --module-test-util
```

#### 按照功能拆分

在电商系统中如下 module

```
--module-test
 --module-test-common 公共部分
 --module-test-order 订单
 --module-test-checkout 购物车
 --module-test-pay 支付
 --module-test-catory 类目
 --module-test-product 商品
 --module-test-price 价格
 --module-test-account 账号
```

#### 工程结构

阿里爸爸推荐的分层架构如下，默认上层依赖下层，箭头关系表示可直接依赖
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/maven/maven-multimodule.png %}
| 各层名称 | 含义 |
| :----------: | :----------------------------------------------------------: |
| 开放 API 层 | 可直接封装 Service 接口暴露成 RPC 接口；通过 Web 封装成 http 接口；网关控制层等 |
| 终端显示层 | 各个端的模板渲染并执行显示的层。 当前主要是 velocity 渲染， JS 渲染， JSP 渲染，移动端展示等。 |
| Web 层 | 主要是对访问控制进行转发，各类基本参数校验，或者不复用的业务简单处理等 |
| Service 层 | 相对具体的业务逻辑服务层 |
| Manager 层 | 通用业务处理层 |
| DAO 层 | 数据访问层，与底层 MySQL、 Oracle、 Hbase、 OB 等进行数据交互 |
| 第三方服务 | 包括其它部门 RPC 服务接口，基础平台，其它公司的 HTTP 接口，如淘宝开放平台、支付宝付款服务、高德地图服务等 |
| 外部数据接口 | 外部（应用）数据存储服务提供的接口，多见于数据迁移场景中 |

:sparkles:Manager 层有如下特征：

1. 对第三方平台封装的层，预处理返回结果及转化异常信息，适配上层接口
2. 对 Service 层通用能力的下沉，如缓存方案，中间件通用处理
3. 与 DAO 层交互，对多个 DAO 的组合复用

#### 分层领域模型规约

|             模型             |                                            含义                                             |
| :--------------------------: | :-----------------------------------------------------------------------------------------: |
|      DO（ Data Object）      |                 此对象与数据库表结构一一对应，通过 DAO 层向上传输数据源对象                 |
| DTO（ Data Transfer Object） |                      数据传输对象， Service 或 Manager 向外传输的对象                       |
|    BO（ Business Object）    |                    业务对象， 可以由 Service 层输出的封装业务逻辑的对象                     |
|            Query             | 数据查询对象，各层接收上层的查询请求。 注意超过 2 个参数的查询封装，禁止使用 Map 类来传输。 |
|      VO（ View Object）      |                      显示层对象，通常是 Web 向模板渲染引擎层传输的对象                      |

:notes:不要使用不稳定的工具包或者 Utils 类, 不稳定指的是提供方无法做到向下兼容，在编译阶段正常，但在运行时产生异常，因此，尽量使用业界稳定的二方工具包

## 构建多模块应用

搭建多模块项目，需要使用 maven 的**继承**和**聚合**

:sparkles:Maven 中聚合和继承的特点
{% alert success no-icon %}

- 聚合负责将多个模块集中在一起进行管理
- 继承则负责各子模块中的公共配置

{% endalert %}

### 创建项目

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210902215119534.png %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210902215636541.png %}

### 修改父工程

{% alert danger no-icon %}

:notes:需要删除 src 目录下的所有内容，最终效果如下：

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210902215741035.png %}

### 创建子模块

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210902215915860.png %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210902215119534.png %}

{% alert danger no-icon %}

注意变化

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210902220036026.png %}

{% alert warning no-icon %}

子模块创建完成如下：

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210902220412189.png %}

### 父工程 pom

此时父工程的`pom.xml`文件发生了如下变化

```xml
<groupId>org.example</groupId>
<artifactId>crowfunding</artifactId>
<packaging>pom</packaging>
<version>1.0-SNAPSHOT</version>
<modules>
    <module>crowfundint-01-admin-parent</module>
</modules>
```

:notes:module 的值是子模块相对于当前 POM 的路径

:sparkles:更改打包方式为`pom`的原因
{% alert info no-icon %}

pom 是最简单的打包类型，不像 jar 和 war，它生成的构件只有它本身，将`packaging`设置为 pom,意味着没有代码需要测试或者编译，也没有资源需要处理,由于使用了聚合，所以打包方式必须为 pom，否则无法构建

{% endalert %}

### 子模块 pom

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210902230507544.png %}

```xml
    <parent>
        <artifactId>crowdfunding</artifactId>
        <groupId>com.pineappleman</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>crowdfunding-01-admin-parent</artifactId>
```

:notes:声明了该模块继承自`com.pineappleman:crowdfunding:1.0-SNAPSHOT`,其实这里面还省略了 `<relativePath></relativePath>` 由于 **relativePath** 默认是 `../pom.xml` 而我们的子项目确实在父项目的下一级目录中，所以是可以不用填写的。**artifactId** 是子模块的组件 id，由于继承了父 pom，所以**groupId**、**version** 也可以不写，默认继承自父 pom
{% alert info no-icon %}

:sparkles:Maven 首先在当前构建项目的环境中查找父 pom，然后项目所在的文件系统查找，然后是本地存储库，最后是远程 repo

{% endalert %}

## 设置父工程编译级别

{% alert success no-icon %}

:sparkles:项目中会统一使用 JDK 版本和编译级别，所以项目的编译级别必须统一，那么将编译插件添加到父工程，子模块依然会无条件去继承父工程的插件

{% endalert %}

### 修改父工程 pom 文件

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

### 查看指定编译级别后

在 `File -> Settings -> Build, Execution, Deployment -> Compiler -> Java Compiler `查看

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210902231341998.png %}

## Maven 依赖版本管理

Maven 中另一个重点的就是依赖管理，接下来详细讲解如何在 idea 中进行依赖管理

### 父工程统一管理依赖

:book:在父工程`<dependencies>`标签中添加三方依赖，子模块会无条件继承父工程所有依赖，例如在父工程的`pom.xml`中加入下面的依赖，最终每一个子模块都会拥有这个依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.2.8</version>
    </dependency>
</dependencies>
```

子模块的依赖如下：
{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210902233256349.png %}

### 父工程管理依赖版本号

{% alert success no-icon %}

通过以上依赖方式，子模块会无条件继承父工程的所有依赖，导致的问题是：不需要继承的依赖也会被继承，最终将<font style="color:red;font-weight:bold">增加项目最终打包的大小，可能为项目上线埋下隐患</font>

{% endalert %}

:sparkles:父工程管理的是所有项目模块的依赖，而不是某一个项目模块的依赖，所以某一个项目模块不需要继承父工程中的所有依赖。子项目模块向父工程声明需要的依赖即可（**声明式依赖**），此时父工程只需要管理依赖的版本号即可

#### 父工程添加 dependencyManagement 标签管理依赖

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

:notes: 由于父工程通过 dependencyManagement 标签管理依赖，之前子模块无条件继承的依赖就全部消失，仅用来声明依赖，并不不导入，只有子模块真正需要使用的时候才导入依赖（`<dependency>`在子模块中详细声明）

:sparkles:`dependencyManagement `特点
{% alert success no-icon %}

- 子项目不会继承 dependencyManagement 组件中声明的依赖
- 但如果子项目想导入某个父 pom 中 dependencyManagement 中的依赖，只需要填写 **groupId 和 artifactId** ,不需要填写版本号，maven 会自动去父 pom 的 dependencyManagement 中找对应的 version，包括 scope、exclusions 等

{% endalert %}

#### 父工程添加 properties 管理版本号

{% alert success no-icon %}

在 properties 标签中，同样可以自定义标签名称来管理依赖的版本号，通常自定义的标签名称由`项目名称-version 构成`，被管理的依赖版本号由`${标签名称}`来代替

{% endalert %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210903000526903.png %}

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210903000531850.png %}

:notes: Maven 会沿着父子层次向上走，直到找到一个拥有 dependencyManagement 组件的项目，然后在其中查找，如果找到则返回申明的依赖，没有继续往上找

#### 子模块声明式添加依赖

由于父工程管理依赖的版本号，那么子模块要想继承依赖，只能通过声明式来添加依赖，实际上，子模块中的依赖是继承父工程依赖的版本号；如果子模块已定义依赖版本号，那么以子模块定义的版本号为准

{% image fancybox fig-100  center https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/image-20210903000601237.png %}

### `dependencies`与`dependencyManagement`的区别

{% alert success no-icon %}
`dependencies` 引入依赖，即时子项目不写`dependencies`，子项目仍然会从父项目中继承`dependencies`中的所有依赖项

{% endalert %}

{% alert success no-icon %}

`dependencyManagement` 声明依赖，并不引入依赖，子项目默认不会继承父项目`dependencyManagement`中的依赖。只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承（version，exclusions，scope 等继承自父 pom）。子项目如果指定了依赖的具体版本号，会优先使用子项目中指定版本，不会继承父 pom 中申明的依赖

{% endalert %}

## 附录

[Maven 多模块管理](https://juejin.cn/post/6844903970024980488)
[maven 依赖版本管理](https://juejin.cn/post/6844903965444816903)
[Maven 与 gradle 的对比](https://zhuanlan.zhihu.com/p/21394120)
[Gradle 与 Maven 的区别](https://blog.csdn.net/Ginny_2017/article/details/105429394)
[Maven 报错：The packaging for this project did not assign a file to the build artifact](https://blog.csdn.net/gao_zhennan/article/details/89713407)
[Spring Boot Maven Plugin 打包异常及三种解决方法：Unable to find main class](https://www.cnblogs.com/thinking-better/p/7827368.html)
[阿里巴巴开发手册](https://developer.aliyun.com/topic/download?spm=a2c6h.20345107.J_8234604580.4.60dd17dbhrCEq2&id=805)
