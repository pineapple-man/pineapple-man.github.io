---
title: npm使用手册
toc: true
clearReading: true
thumbnailImagePosition: bottom
metaAlignment: center
thumbnailImage: https://gitee.com/mingchaohu/blog-image/raw/master/image/npm.png
categories: 其他
tags: Web
keywords:
excerpt: 本文用于记录 npm 工具的常用操作
---
## 环境配置

主要进行前端`npm`以及`node`的配置，默认终端处于项目根目录下

### 查看npm,node版本

由于前端项目具有明确的版本要求，然而使用Linux系统软件源进行安装的版本比较落后，此时进行项目的运行通常会产生诸多问题，因此需要进行软件的升级

```shell
npm -v
node -v
```

### 配置淘宝npm镜像源

访问一部分外围镜像时延较长，因此可以选用国内的npm镜像下载

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

而后使用`npm`进行的命令都可以相应的换成`cnpm`使用

> FAQ
>
> 也许是淘宝不经常维护这个网站，通常使用cnpm安装包时依旧存在一些依赖关系。此时，仍然需要使用npm进行安装

### 使用npm命令管理模组

```shell
npm install <Module Name>		#安装模组
npm uninstall <Module Name>		#卸载相应的模组
```

> FAQ
>
> ​	如何不指定模组姓名，会在当前目录中查找`package.json`解析包之间的依赖关系，从而进行包的安装

### 安装配置node.js

系统给我们提供了对`node.js`的管理工具:`n`

#### n管理工具的安装

```shell
npm install -g n
```

#### 使用n管理node.js

使用`n`工具安装、更新node.js具有两种方式：

1. 直接使用n进行下载安装
2. 自行到官网下载源代码，而后解压在n管理工具的目录下

##### 使用n进行下载安装

```shell
n 13.11.0		#会自动前往node官网下载
n --stable		#安装稳定版
n --latest		#安装最新版
```

软件包下载完成之后，默认已经将下载的node作为当前使用的node，可以`node -v`进行查看检验

##### 通过官网下载安装

访问[官网](https://nodejs.org/dist/)查看相应的软件包

```shell
wget url_path		#访问官网查找相应的软件包下载到本地
tar -xvf node_vxxx.tar.gz		#解压软件包到当前路径
mv ./node_vxxx /usr/local/n/versions/node/xxx		#将自行下载的软件包托管在n的管理目录下，借助n进行管理
n		#选择需要使用的版本，即完成了特定版本node的安装
```

> FAQ
>
> 使用n下载相关软件时，中途不要出现中断下载任务等情况，否则最终会由于node下载不完整，而n盲目的将node版本切换过去，系统将会出现`Segmentation fault`.此时，需要借助n切换node的版本，然后删除出现故障的版本

```shell
n		#选择完好的node版本进行切换
n rm 13.11.0 #删除特定版本的node
```

### 常见问题解答

#### 1.迁移项目的重新配置

如果项目只是简单的拷贝，则在运行`npm run dev`时可能会出现`Node Sass does not yet support your current environment:...`问题。这是由于缺少了相关依赖造成的，因此需要重新编译相关源代码

```shell
npm rebuild node-sass
#如果问题没有解决,将组件删除之后重新安装
npm uninstall --save node-sass
npm cache clean
npm install --save node-sass
```

#### 2.Error: Cannot find module 'semver'

首先升级了`node`使得`npm`和`node`版本不匹配，造成最终能够使用node但是不能使用npm.

> - 解决方法：
>   - 将安装的最新版node以及旧版的npm应用均删除.再根据能够使用npm选择是否重新安装。

```shell
which npm
which node
rm -rf /usr/local/bin/npm
rm -rf /usr/local/bin/node
```

## C盘清理，移动node 依赖和缓存文件

https://blog.csdn.net/qq_34817440/article/details/104260701

## No parser and no filepath given, using 'babel' the parser now but this will throw an error in t he f
https://blog.csdn.net/weixin_41888813/article/details/101345744

## node-sass 安装失败报错的原因及解决办法(整理)
https://www.cnblogs.com/gitnull/p/10188030.html