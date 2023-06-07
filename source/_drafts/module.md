---
title: module
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
thumbnailImage:
categories: [Kernel]
tags: [Kernel, module]
keywords: [Kernel, module]
excerpt: 内核模块开发
---

## 概述

<!-- prettier-ignore-start -->
## __init 与 __exit
<!-- prettier-ignore-end -->

```C
static int __init hello_init(void) {
    // 初始化代码
}

```

这是一个常见且非常简单的模块初始化定义函数，其中`__init`是一个特殊的宏，用于告诉内核将该函数标记为初始化函数。在内核加载模块时，内核会检测到这个宏并调用相应的函数完成模块的初始化工作，例如，注册设备驱动、初始化数据结构、分配内存等。一旦初始化完成，这些函数所占用的内存空间就会被释放，以便给其他模块或系统使用。
`__init`宏的作用是告诉编译器将标记的函数放在特殊的内存段中，该段在初始化完成后会被释放。这样可以减少系统启动时的内存占用，并提高系统的运行效率。
它用于指定一个函数只在系统初始化阶段执行，并在初始化完成后释放相关资源。
需要注意的是，`__init`标记只在内核编译时有效，在用户空间的代码中并不会产生任何影响。
与之相应的是`__exit`宏，用于标记模块的退出函数（exit function）。它用于指定一个函数只在模块被卸载时执行，用于清理模块初始化时分配的资源。
当内核模块被卸载时，内核会执行标记为`__exit`的函数，这些函数通常用于释放模块初始化时分配的资源，关闭设备、注销驱动程序等清理操作。与`__init`不同，`__exit`标记的函数并不会在模块初始化完成后自动释放内存，而是在模块被卸载时才执行。
`__exit`宏的作用是告诉编译器将标记的函数放在特殊的内存段中，该段在模块卸载时会被调用。这样可以确保在模块退出时执行相应的清理工作，以避免资源泄露或其他问题。
与`__init`相同`__exit`宏标记只在内核编译时有效，在用户空间的代码中并不会产生任何影响。

这些宏，用于将 Linux 代码的某些部分定位到最终执行的二进制文件中的特殊区域，指示编译器以特殊方式标记这个函数，在最后，链接器会收集所有的函数在二进制文件的末尾（或开头）带有这个标记的所有函数。

```C
/* These macros are used to mark some functions or
 * initialized data (doesn't apply to uninitialized data)
 * as `initialization' functions. The kernel can take this
 * as hint that the function is used only during the initialization
 * phase and free up used memory resources after
 *
 * Usage:
 * For functions:
 *
 * You should add __init immediately before the function name, like:
 *
 * static void __init initme(int x, int y)
 * {
 *    extern int z; z = x * y;
 * }
 *
 * If the function has a prototype somewhere, you can also add
 * __init between closing brace of the prototype and semicolon:
 *
 * extern int initialize_foobar_device(int, int, int) __init;
 *
 * For initialized data:
 * You should insert __initdata or __initconst between the variable name
 * and equal sign followed by value, e.g.:
 *
 * static int init_variable __initdata = 0;
 * static const char linux_logo[] __initconst = { 0x32, 0x36, ... };
 *
 * Don't forget to initialize data not at file scope, i.e. within a function,
 * as gcc otherwise puts the data into the bss section and not into the init
 * section.
 */

/* These are for everybody (although not all archs will actually
   discard it in modules) */
#define __init		__section(".init.text") __cold  __latent_entropy __noinitretpoline __nocfi
```
这个宏将`__init`标记的函数放置在`.init.text`段中，表示这些函数是内核的初始化代码。这些函数在内核启动时被调用，用于执行模块的初始化和系统的启动配置。
`__init`宏的详细定义和用法，可以在`/include/linux/init.h`文件中查看。
## 附录

[/include/linux/module.h](https://elixir.bootlin.com/linux/v5.15/source/include/linux/module.h)
[/include/linux/init.h](https://elixir.bootlin.com/linux/v5.15/source/include/linux/init.h)
[在 Linux 内核代码中，`__init` 是什么意思？](https://www.qiniu.com/qfans/qnso-8832114)
[linux kernel `__init` 和`__exit` 宏的作用](https://www.cnblogs.com/linengier/p/12380780.html)
