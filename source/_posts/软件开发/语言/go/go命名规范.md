---
title: go 命名规范
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Go
tags: GO
keywords: 命名规范
excerpt: 本文主要记录 Golang 中变量的命令规范
date: 2022-04-04 14:23:32
thumbnailImage:
---
<!-- toc -->


命名规则涉及变量、常量、全局函数、结构、接口、方法等的命名

## 概述

Go是一门区分大小写的语言, Go语言从语法层面进行了以下限定：<font style="color:red;font-weight:bold">任何需要对外暴露的名字必须以大写字母开头，不需要对外暴露的则应该以小写字母开头</font>
{% alert success no-icon %}

- 命名如果（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：Expose，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 public）；

- 命名如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 private ）

{% endalert %}

## Go 包名称

Go 中 package 的名字最好和目录保持一致，并且应该是<font style="color:red;font-weight:bold">小写</font>的单词，尽量做到见名知其意思，<font style="color:red;font-weight:bold">不要使用下划线或者混合大小写</font>

## Go 文件命名

尽量采取有意义的文件名，简短，有意义，应该为小写单词，使用下划线分隔各个单词。


## 结构体命名

采用驼峰命名法，首字母根据访问控制大写或者小写，最好将结构体的申明和初始化在多行情况下进行，例如
```golang
type ClassFile struct {
	magic        uint32 
	minorVersion uint16
	majorVersion uint16
	constantPool ConstantPool
	accessFlags  uint16
	thisClass    uint16
	superClass   uint16
	interfaces   []uint16
	fields       []*MemberInfo
	methods      []*MemberInfo
	attributes   []AttributeInfo
}
```
## 接口命名

接口命名规则和结构体命名规则相似，单个函数的结构体名以`er`作为后缀，例如：`Reader`、`Writer`
```golang
type Reader interface {
    Read(bytes []byte)(length int32,err error)
}
```

## 变量命名

和结构体类似，变量名称一般遵循驼峰法，首字母根据访问控制原则大写或者小写，但遇到特有名词时，需要遵循以下规则：
{% alert success no-icon %}

如果变量类型是 `bool` 类型，则名称应该以`Has`、`Is`等开头
{% endalert %}
## 常量命名

常量均需要使用大写字母组成，并且各个单词之间使用下划线`_`进行分隔
```golang
const PATH_LIST_SEPARATOR = string(os.PathListSeparator)
```

如果是枚举类型的常量，需要见创建相应的类型
```golang
const (
	// CONSTANT_UTF8_INFO UTF-8 编码的字符串
	CONSTANT_UTF8_INFO = 1

	// CONSTANT_INTEGER_INFO 整形字面量
	CONSTANT_INTEGER_INFO = 3
)
```
## 错误处理
{% alert success no-icon %}

- 错误处理的原则就是不能丢弃任何有返回的 err 的调用，不要使用`_`丢弃，必须全部处理，接收到错误，要么返回 err，要么使用 log 记录下来
- 一旦发生错误要尽早返回，防止错误的蔓延
- 错误描述如果是英文，必须为小写，不需要标点结尾
- 采用独立的错误流进行处理

{% endalert %}


```golang
if err!=nil{
    // error handling
    return
}
// normal code
```
## 单元测试

单元测试文件命名规范为`example_test.go`测试用例的函数名称笔试以`Test`开头，例如：文件`entry_dir`中存在一个结构体以及对应的方法，想要对其进行测试
```golang
type DirEntry struct {
	AbsDir string
}
func (e *DirEntry) ReadClass(className string) ([]byte, Entry, error){}
```
需要创建一个名称为`entry_dir_test.go`的文件，并且内部书写的测试方法名称为`TestDirEntry_ReadClass`

## 附录
[Go中的命名规范](https://www.cnblogs.com/rickiyang/p/11074174.html)