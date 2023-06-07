---
title: printk
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: [Kernel]
tags: Kernel
keywords: [Kernel, printk]
excerpt: 最近一直在使用printk文件中的相关方法，想要在这里记录一下，本文主要内容翻译自Linux Kernel 中相关文档。
---

## 概述

本文假定读者已经了解并使用过`C/C++`中`printf()`的相关方法，如果对此不太熟悉，可以[点击这里](https://man7.org/linux/man-pages/man3/printf.3.html)了解`printf(3)`。

C 语言中的 printf 一系列方法用于分别向不同受体根据某种格式，传递消息。

{% blockquote "printf(3)" https://man7.org/linux/man-pages/man3/printf.3.html "Linux manual page" %}

- The functions `printf()` and `vprintf()` write output to stdout, the standard output stream;
- `fprintf()` and `vfprintf()` write output to the given output stream;
- `sprintf()`, `snprintf()`, `vsprintf()`, and `vsnprintf()` write to the character string str.
  {% endblockquote %}

printk 是内核中广为人知的函数之一，它是我们在开发内核工具时用于打印消息的标准工具，通常也是跟踪和调试的最基本方法。printk 的设计和 printf 非常相似，但是两者的功能却是天差地别。

- printk() 具有类似日志级别的概念
- 格式化字符串虽然在很大程度上与 C99 规范兼容，但并不完全遵循此规范，它有一些扩展和一些限制，在[如何正确使用 printk 格式说明]()
- printk() 输出的信息重定向到了内核日志缓冲区中，这是一个通过/dev/kmsg 导出到用户空间的环形缓冲区，可以通过使用`dmesg`命令查看。

## proto

printk() 方法通常使用方式如下:

```C
printk(KERN_INFO "Message: %s\n", arg);
```

其中 KERN_INFO 是内核中的日志级别，它被连接到格式化字符串，日志级别并不是一个单独的参数。当前系统可选的日志级别如下：
|Name|String(Value)|Alias function|
|:---:|:---:|:---:|
|KERN_EMERG|"0"|pr_emerg()|
|KERN_ALERT|"1"|pr_alert()|
|KERN_CRIT|"2"|pr_crit()|
|KERN_ERR|"3"|pr_err()|
|KERN_WARNING|"4"|pr_warn()|
|KERN_NOTICE|"5"|pr_notice()|
|KERN_INFO|"6"|pr_info()|
|KERN_DEBUG|"7"|pr_debug() and pr_devel() if DEBUG is defined|
|KERN_DEFAULT|""|/|
|KERN_CONT|"c"|pr_cont()|

```C
/**
 * printk - print a kernel message
 * @fmt: format string
 *
 * This is printk(). It can be called from any context. We want it to work.
 *
 * If printk indexing is enabled, _printk() is called from printk_index_wrap.
 * Otherwise, printk is simply #defined to _printk.
 *
 * We try to grab the console_lock. If we succeed, it's easy - we log the
 * output and call the console drivers.  If we fail to get the semaphore, we
 * place the output into the log buffer and return. The current holder of
 * the console_sem will notice the new output in console_unlock(); and will
 * send it to the consoles before releasing the lock.
 *
 * One effect of this deferred printing is that code which calls printk() and
 * then changes console_loglevel may break. This is because console_loglevel
 * is inspected when the actual printing occurs.
 *
 * See also:
 * printf(3)
 *
 * See the vsnprintf() documentation for format string extensions over C99.
 */
#define printk(fmt, ...) printk_index_wrap(_printk, fmt, ##__VA_ARGS__)
```

<!-- prettier-ignore-start -->
{% codeblock  "/include/linux/printk.h" lang:c  "https://elixir.bootlin.com/linux/v5.15/source/include/linux/printk.h#L446" "printk macro" %}
#define printk(fmt, ...) printk_index_wrap(_printk, fmt, ##__VA_ARGS__)
{% endcodeblock %}
<!-- prettier-ignore-end -->

## 附录

[linux kernel doc v5.15](https://www.kernel.org/doc/html/v5.15/core-api/printk-basics.html)
[Linux kernel doc How to get printk format specifiers right](https://www.kernel.org/doc/html/latest/core-api/printk-formats.html#how-to-get-printk-format-specifiers-right)
[printf(3) — Linux manual page](https://man7.org/linux/man-pages/man3/printf.3.html)
