---
title: Go 字符串操作合集
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Go
tags: GO
keywords: Go
excerpt: 本文是 Go 中的字符串操作整理篇章
date: 2022-03-29 23:28:40
thumbnailImage:
---

<!-- toc -->

## 字符串修改操作

Go 语言中的字符串无法直接修改每一个字符元素，只能通过重新构造新的字符串并赋值给原来的字符串变量实现

```go
ansKey := "TTFT"
ansKeyBytes := []byte(absKey)
for i := 0;i<len(anskey);i++{
    ansKeyBytes[i] = ' '
}
fmt.Println(string(angleBytes))
```

{% alert success no-icon %}

:notebook: Go 中的字符串和 Java 中相同，默认不能直接修改，需要转变成为字符数组，随后通过对字符数组的修改，最终通过`[]byte`和`string`之间的强制类型转换做到对原本字符串的修改

{% endalert %}

## 附录

[Go 语言修改字符串](https://www.cnblogs.com/niuben/p/12523396.html)
[Go 语言字符串常见操作的使用汇总](https://www.jb51.net/article/245112.htm)
[Go 语言几种字符串的拼接方式比较](https://segmentfault.com/a/1190000040275250)
[go 语言怎么将 string 转 int 类型](https://www.php.cn/be/go/472227.html)
