---
title: go 命令记录
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2022-04-27 19:46:56
thumbnailImage:
categories: Go
tags: GO
keywords: Go命令
excerpt: 使用 Java 时完全没怎么在意 Java 命令的形式，但是最近在进行 go 开发时，却发现需要使用很多的 Go 命令，在这里记录一下
---
> 本文大部分内容来自于网络摘录，如果想要查看原文，请直接通过附录访问


<!-- toc -->
## 概述

Go 有非常多的命令，本文想要将平时使用到的命令都列举在这里，以发方便后续查看

## `go build` 命令

{% alert info no-icon %}

`go build`命令用于编译我们指定的源码文件或代码包以及它们的依赖包

{% endalert %}

### `go build` 编译当前目录
如果我们在执行 go build 命令后不跟任何代码包，那么命令将试图编译当前目录所对应的代码包。

{% alert warning no-icon %}

例如，我们想编译某个项目中的`logging`代码包。其中一个方法是进入`logging`目录并直接执行下列命令就会编译当前目录下的所有**命令码源文件**

```bash
pineapple-man@localhost:~/golang/project/src/logging$ go build
```
{% endalert %}

上面提出了一个名词：**命令码源文件**，在Go语言的源码文件有三大类，即：命令源码文件、库源码文件和测试源码文件。他们的功用各不相同，而写法也各有各的特点。
{% alert success no-icon %}

- 命令源码文件总是作为可执行的程序的入口。命令源码文件总应该属于main代码包，且在其中有无参数声明、无结果声明的main函数。单个命令源码文件可以被单独编译，也可以被单独安装（可能需要设置环境变量GOBIN）。当然，命令源码文件也可以被单独运行。
- 库源码文件一般用于集中放置各种待被使用的程序实体（全局常量、全局变量、接口、结构体、函数等等）。
- 测试源码文件主要用于对前两种源码文件中的程序实体的功能和性能进行测试。另外，后者也可以用于展现前两者中程序的使用方法。

{% endalert %}
### `go build` 编译指定目录
另一种编译`logging`包的方法是：在任意目录下，通过指定`logging`目录做到编译的目的

{% alert warning no-icon %}
```bash
# 假设当前在 #GOPATH/src 目录下
pineapple-man@localhost:~/golang/project/src$ go build logging
```
这种编译方法可以正常执行是因为已经在环境变量`GOPATH`中加入了goc2p项目的根目录（即~/golang/goc2p/）。这时，goc2p项目的根目录就成为了一个工作区目录。只有这样，Go语言才能正确识别我们提供的goc2p项目中某个代码包的导入路径。

而代码包的导入路径是指，相对于Go语言自身的源码目录（即$GOROOT/src）或我们在环境变量GOPATH中指定的某个目录的src子目录下的子路径。例如，这里的代码包logging的绝对路径是~/golang/goc2p/src/logging。而不论goc2p项目的根文件夹被放在哪儿，logging包的导入路径都是logging。显而易见，我们在称呼一个代码包的时候总是以其导入路径作为其称谓。

{% endalert %}
### `go build` 编译指定多个源文件

go build 还可以同时编译指定的多个 Go 源码文件

```bash
pineapple-man@localhost:~/golang/goc2p/src$ go build \
    logging/base.go  logging/console_logger.go logging/tag.go
```
但是，使用这种方法会有一个限制。作为参数的多个 Go 源码文件必须在同一个目录中。也就是说，如果想用这种方法既编译 logging 包又编译 basic 包是不可能的。但是在需要的时候，那些被编译目标依赖的代码包会被 go build 命令自动的编译。
{% alert warning no-icon %}

例如：如果有一个导入路径为 app 的代码包，同时依赖了 logging 包和 basic 包。那么在执行 go build app 的时候，该命令就会自动的在编译 app 包之前去检查 loggin g包和 basic 包的编译状态。如果发现它们的编译结果文件不是最新的，那么该命令就会先去的编译这两个代码包，然后再编译 app 包

{% endalert %}
`go build` 命令在编译只包含库源码文件的代码包（或者同时编译多个代码包）时，只会做检查行的编译，而不会输出任何结果文件。

{% alert info no-icon %}

一方面，go build 既不能编译包含 「 多个命令源码文件的包 」，也不能同时编译 「 多个命令源码文件 」。因为，如果把多个命令源码文件作为一个整体看待，那么每个文件中的 main 函数就属于重名函数，在编译时会抛出重复定义错误。

{% endalert %}


{% alert warning no-icon %}

假如，在goc2p项目的代码包cmd（此代码包仅用于示例目的，并不会永久存在于该项目中）中包含有两个命令源码文件：showds.go和initpkg_demo.go，那么我们在使用go build命令同时编译它们时就会失败。示例如下：

```bash
pineapple-man@localhost:~/golang/goc2p/src/cmd$ go build showds.go initpkg_demo.go
# command-line-arguments
./initpkg_demo.go:19: main redeclared in this block
        previous declaration at ./showds.go:56
```

:notes: 需要注意命令给出的提示有`command-line-arguments`,在这个位置上原本应该显示的是作为编译目标的源码文件所属的代码包的导入路径。但是，这里显示的并不是它们所属的代码包的导入路径cmd。这是因为，命令程序在分析参数的时候如果发现第一个参数是Go源码文件而不是代码包，则会在内部生成一个「 虚拟代码包 」。这个虚拟代码包的导入路径和名称都会是`command-line-arguments`。在其他基于编译流程的命令程序中也有与之一致的操作，比如`go install`命令和`go run`命令。

{% endalert %}

另一方面，如果我们编译的多个属于main包的源码文件中没有main函数的声明，那么就会使编译器立即报出「 未定义main函数声明 」的错误并中止编译。也就是说，在我们同时编译多个main包的源码文件时，要保证其中有且仅有一个 main 函数声明，否则编译是无法成功的。

如果我们有多个声明为属于main包的源码文件，且其中只有一个文件声明了main函数的话，那么是可以使用`go build`命令同时编译它们的。在这种情况下，不包含main函数声明的那几个源码文件会被视为库源码文件。如此编译后的结果文件的名称将会与我们指定的编译目标中最左边的那个源码文件的主文件名相同。

### 额外参数

除了让Go语言编译器自行决定可执行文件的名称，还可以自定义输出文件名称。示例如下：

```bash
pineapple-man@localhost:~/golang/goc2p/src/basic/pkginit$ go build -o initpkg initpkg_demo.go 
pineapple-man@localhost:~/golang/goc2p/src/basic/pkginit$ ls
initpkg    initpkg_demo.go
```
{% alert success no-icon %}

使用`-o`标记可以指定输出文件（在这个示例中指的是可执行文件）的名称。它是最常用的一个go build命令标记。但需要注意的是，当使用标记-o的时候，不能同时对多个代码包进行编译。

{% endalert %}
{% alert success no-icon %}

标记`-i`会使 go build 命令安装那些编译目标依赖的且还未被安装的代码包。这里的安装意味着产生与代码包对应的归档文件，并将其放置到当前工作区目录的 pkg 子目录的相应子目录中。在默认情况下，这些代码包是不会被安装的。

{% endalert %}

除此之外，还有一些标记不但受到 go build 命令的支持，而且对于后面会提到的 go install、go run、go test 等命令同样是有效的。下表列出了其中比较常用的标记。

<style type="text/css">
.tg  {border-collapse:collapse;border-color:#9ABAD9;border-spacing:0;margin:0px auto;}
.tg td{background-color:#EBF5FF;border-color:#9ABAD9;border-style:solid;border-width:1px;color:#444;
  font-family:Arial, sans-serif;font-size:14px;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{background-color:#409cff;border-color:#9ABAD9;border-style:solid;border-width:1px;color:#fff;
  font-family:Arial, sans-serif;font-size:14px;font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-sx1p{font-weight:bold;position:-webkit-sticky;position:sticky;text-align:center;top:-1px;vertical-align:middle;
  will-change:transform}
.tg .tg-nrix{text-align:center;vertical-align:middle}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-sx1p">标记名称</th>
    <th class="tg-sx1p">标记描述</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-nrix">-a</td>
    <td class="tg-nrix">强行对所有涉及到的代码包（包含标准库中的代码包）进行重新构建，即使它们已经是最新的了。</td>
  </tr>
  <tr>
    <td class="tg-nrix">-n</td>
    <td class="tg-nrix">打印编译期间所用到的其它命令，但是并不真正执行它们。</td>
  </tr>
  <tr>
    <td class="tg-nrix">-p n</td>
    <td class="tg-nrix">指定编译过程中执行各任务的并行数量（确切地说应该是并发数量）。<br>在默认情况下，该数量等于CPU的逻辑核数。但是在<span style="color:#2D85CA;background-color:#F9F2F4">darwin/arm</span>平台（即iPhone和iPad所用的平台）下，该数量默认是<span style="color:#2D85CA;background-color:#F9F2F4">1</span>。</td>
  </tr>
  <tr>
    <td class="tg-nrix">-race</td>
    <td class="tg-nrix"><span style="font-weight:400;font-style:normal">开启竞态条件的检测。</span><br><span style="font-weight:400;font-style:normal">不过此标记目前仅在</span>linux/amd64、freebsd/amd64、darwin/amd64和windows/amd64平台下受到支持。</td>
  </tr>
  <tr>
    <td class="tg-nrix">-v</td>
    <td class="tg-nrix">打印出那些被编译的代码包的名字。</td>
  </tr>
  <tr>
    <td class="tg-nrix">-work</td>
    <td class="tg-nrix">打印出编译时生成的临时工作目录的路径，并在编译结束时保留它。在默认情况下，编译结束时会删除该目录。</td>
  </tr>
  <tr>
    <td class="tg-nrix">-x</td>
    <td class="tg-nrix">打印编译期间所用到的其它命令。注意它与<span style="color:#2D85CA;background-color:#F9F2F4">-n</span>标记的区别。</td>
  </tr>
</tbody>
</table>

执行 go build 命令的计算机如果拥有多个逻辑CPU核心，那么编译代码包的顺序可能会存在一些不确定性。但是，它一定会满足这样的约束条件：「 依赖代码包 -> 当前代码包 -> 触发代码包 」

### `buildmode`

在 go build 和 go install 命令中，我们可以指定 -buildmode 参数来让编译器构建出特定的对象文件。通过命令 go help buildmode，可以看到其支持的选项：
```bash
-buildmode=archive
-buildmode=c-archive
-buildmode=c-shared
-buildmode=default
-buildmode=shared
-buildmode=exe
-buildmode=pie
-buildmode=plugin
```
#### plugin

> build the listed main packages,plus all packages that they import,into a Go plugin.Package not named main are ignored

它将 package main 编译为一个 go 插件，并可在运行时动态加载
```golang
package main

import "fmt"

type greeting string

func (g greeting) Greet() {
	fmt.Println("hello world")
}

var Greeter greeting
```
随后将其编译成为一个 go 插件
```bash
go build -buildmode=plugin -o greeter.so greeter.go
```
最终再使用 golang 官方的 plugin 库来载入这个插件
```golang
package main

import (
	"fmt"
	"os"
	"plugin"
)

type Greeter interface {
	Greet()
}

func main() {
  //载入插件
	plug, err := plugin.Open("./greeter.so")
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}
  //在插件中寻找对应的方法
	symGreeter, err := plug.Lookup("Greeter")
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}

	var greeter Greeter
	greeter, ok := symGreeter.(Greeter)
	if !ok {
		fmt.Println(err)
		os.Exit(1)
	}
	greeter.Greet()
}
```

## `go install`
{% alert info no-icon %}

命令go install用于编译并安装指定的代码包及它们的依赖包。当指定的代码包的依赖包还没有被编译和安装时，该命令会先去处理依赖包，随后再安装。

{% endalert %}

与go build命令一样，传给go install命令的代码包参数应该以导入路径的形式提供。并且，go build命令的绝大多数标记也都可以用于go install命令。实际上，go install命令只比go build命令多做了一件事，即：**安装编译后的结果文件到指定目录**

### 安装代码包

如果go install命令后跟的代码包中仅包含库源码文件，那么go install命令会把「 编译后的结果 」文件保存在源码文件所在工作区的pkg目录下。对于仅包含库源码文件的代码包来说，这个结果文件就是将对应的代码包归档文件（也叫静态链接库文件，名称以.a结尾）

相比之下，我们在使用go build命令对仅包含库源码文件的代码包进行编译时，是不会在当前工作区的src目录以及pkg目录下产生任何结果文件的。结果文件会出于编译的目的被生成在临时目录中，但并不会使当前工作区目录产生任何变化。

{% alert success no-icon %}

如果我们在执行go install命令时不后跟任何代码包参数，那么命令将试图编译当前目录所对应的代码包。如果指定了需要安装的代码包，首先会对指定代码包先编译，随后会将编译的结果安装到默认目录($GOPATH/pkg)下

{% endalert %}

{% alert warning no-icon %}

例如，在代码包pkgtool对应的目录下安装cnet/ctcp，下面命令中我们使用了一个目录相对路径

```bash
pineapple-man@localhost:~/golang/goc2p/src/pkgtool$ go install -a -v -work ../cnet/ctcp
WORK=/tmp/go-build083178213
```

{% endalert %}

实际上，这种提供代码包位置的方式被叫做本地代码包路径方式，也是被所有Go命令接受的一种方式，这包括之前已经介绍过的go build命令。但是需要注意的是，{% hl_text red %}本地代码包路径只能以目录相对路径的形式呈现，而不能使用目录绝对路径 {% endhl_text %}。请看下面的示例：
```bash
pineapple-man@localhost:~/golang/goc2p/src/cnet/ctcp$ go install -v -work ~/golang/goc2p/src/cnet/ctcp
can't load package: package /home/pineapple-man/golang/goc2p/src/cnet/ctcp: import "/home/pineapple-man/golang/goc2p/src/cnet/ctcp": cannot import absolute path
```
以目录绝对路径的形式提供代码包位置是不会被 Go 命令认可的。这是由于 Go 认为本地代码包路径的表示只能以`./`或`../`开始，再或者直接为`.`或`..`，而代码包的代码导入路径又不允许以`/`开始。所以，这种用绝对路径表示代码包位置的方式也就不能被支持了，这个规则适用于所有 go 命令。

### 安装命令源码文件

{% alert success no-icon %}

除了安装代码包之外，go install命令还可以安装命令源码文件。

{% endalert %}
```bash
pineapple-man@localhost:~/golang/goc2p/src$ go install helper/ds/showds.go
go install: no install location for .go files listed on command line (GOBIN not set)
```

在环境变量GOPATH中包含多个工作区目录路径时，我们需要在编译命令源码文件前先对环境变量GOBIN进行设置。实际上，这个环境变量所指的目录路径就是命令程序生成的结果文件的存放目录。go install命令会把相应的可执行文件放置到这个目录中。

由于命令go build在编译库源码文件后不会产生任何结果文件，所以自然也不用会在意结果文件的存放目录。在该命令编译单一的命令源码文件或者包含一个命令源码文件和多个库源码文件时，在结果文件存放目录无效的情况下会将结果文件（也就是可执行文件）存放到执行该命令时所在的目录下。因此，即使环境变量GOBIN的值无效，我们在执行go build命令时也不会见到这个错误提示信息。
{% alert success no-icon %}

然而，go install命令中一个很重要的步骤就是将结果文件（归档文件或者可执行文件）存放到相应的目录中。所以，go install命令在安装命令源码文件时，如果环境变量GOBIN的值无效，则它会在最后检查结果文件存放目录的时候发现这一问题，并打印与上述示例所示内容类似的错误提示信息，最后直接退出。

{% endalert %}

而且，在我们为环境变量GOBIN设置了正确的值之后，这个错误提示信息仍然可能会出现。这是因为，只有在安装命令源码文件的时候，命令程序才会将环境变量GOBIN的值作为结果文件的存放目录。而在安装库源码文件时，在命令程序内部的代表结果文件存放目录路径的那个变量不会被赋值。最后，命令程序会发现它依然是个无效的空值。所以，命令程序会同样返回一个关于「 无安装位置 」的错误。这就引出一个结论，我们只能使用安装代码包的方式来安装库源码文件，而不能在go install命令罗列并安装它们。另外，go install命令目前无法接受标记-o以自定义结果文件的存放位置。这也从侧面说明了{% hl_text red %} go install命令不支持针对库源码文件的安装操作{% endhl_text %}。

## `go get`
{% alert success no-icon %}

命令`go get`可以根据要求和实际情况从互联网上下载或更新指定的代码包及其依赖包，并对它们进行编译和安装。在上面这个示例中

{% endalert %}

为了分离自己与第三方的代码，我们会设置两个或更多的工作区。我们现在有一个目录路径为/home/pineapple-man/golang/lib的工作区，并且它是环境变量GOPATH值中的第一个目录路径。注意，环境变量GOPATH中包含的路径不能与环境变量GOROOT的值重复。好了，如果我们使用go get命令下载和安装代码包，那么这些代码包都会被安装在上面这个工作区中。我们暂且把这个工作区叫做Lib工作区。

另一方面，如果我们想把一个项目上传到Github网站（或其他代码托管网站）上并被其他人使用的话，那么我们就应该把这个项目当做一个代码包来看待。其实我们在之前已经提到过原因，go get命令会将项目下的所有子目录和源码文件存放到第一个工作区的src目录下，而src目录下的所有子目录都会是某个代码包导入路径的一部分或者全部。也就是说，我们应该直接在项目目录下存放子代码包和源码文件，并且直接存放在项目目录下的源码文件所声明的包名应该与该项目名相同（除非它是命令源码文件）。这样做可以让其他人使用go get命令从Github站点上下载你的项目之后直接就能使用它。

### 远程导入路径分析

实际上，go get命令所做的动作也被叫做代码包远程导入，而传递给该命令的作为代码包导入路径的那个参数又被叫做代码包远程导入路径

go get命令不仅可以从像Github这样著名的代码托管站点上下载代码包，还可以从任何命令支持的代码版本控制系统（英文为Version Control System，简称为VCS）检出代码包。任何代码托管站点都是通过某个或某些代码版本控制系统来提供代码上传下载服务的。所以，更严格地讲，go get命令所做的是从代码版本控制系统的远程仓库中检出/更新代码包并对其进行编译和安装。

对于已存在于本地工作区的代码包，除非要求强行更新代码包，否则go get命令不会进行重复下载。如果想要强行更新代码包，可以在执行go get命令时加入-u标记。

## `go clean`

{% alert info no-icon %}

执行go clean命令会删除掉执行其它命令时产生的一些文件和目录，包括：

1. 在使用go build命令时在当前代码包下生成的与包名同名或者与Go源码文件同名的可执行文件。在Windows下，则是与包名同名或者Go源码文件同名且带有“.exe”后缀的文件
2. 如果执行go clean命令时带有标记-i，则会同时删除安装当前代码包时所产生的结果文件。如果当前代码包中只包含库源码文件，则结果文件指的就是在工作区的pkg目录的相应目录下的归档文件。如果当前代码包中只包含一个命令源码文件，则结果文件指的就是在工作区的bin目录下的可执行文件。
3. 如果执行go clean命令时带有标记-r，则还包括当前代码包的所有依赖包的上述目录和文件。

{% endalert %}

## `go doc` & `godoc`

{% alert info no-icon %}

go doc命令可以打印附于Go语言程序实体上的文档。我们可以通过把程序实体的标识符作为该命令的参数来达到查看其文档的目的。

{% endalert %}

## `go run`

{% alert info no-icon %}

`go run` 命令用于运行命令源码文件

{% endalert %}

go run命令可以编译并运行命令源码文件。由于它其中包含了编译动作，因此它也可以接受所有可用于go build命令的标记。除了标记之外，go run命令只接受Go源码文件作为参数，而不接受代码包。与go build命令和go install命令一样，go run命令也不允许多个命令源码文件作为参数，即使它们在同一个代码包中也是如此。

## 附录
[go 命令教程](https://wiki.jikexueyuan.com/project/go-command-tutorial/0.1.html)