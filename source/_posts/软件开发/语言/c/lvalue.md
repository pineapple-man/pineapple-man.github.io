---
title: '深入 C 语言: lvalue'
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories:
  - C
tags:
  - C
keywords:
  - lvalue
  - lvalue conversion
excerpt: 最近遇到一些关于`lvalue`的问题，在这里记录一下
date: 2023-04-07 12:10:41
thumbnailImage:
---
<!-- toc -->


本文部分内容翻译自 c99 specification[^1]，如果觉得不妥的地方可以查看原文了解更加明确的见解
{% alert info no-icon %}

在开始介绍之前有一点需要说明：C 语言不同与 C++ 语言，两者的 `lvalue` 指代并不相同，本文全部的内容基于 C 语言环境

{% endalert %}

## 概述

- `lvalue`在 C99 规范中将其称为「定位符值」(`locator value`)，其概念最初是由赋值表达式`E1=E2`引起，此赋值等式的左操作数`E1`必须是可修改的(modifiable)`lvalue`，将`lvalue`看作对象(object)的定位符更加清晰。
- `rvalue`在 C99 规范中描述为「表达式的值」(`value of an expression`)

一种明显使用`lvalue`的例子就是对象的标志符(identifier of an object)。
再举一个例子，如果`E`是一个一元表达式，是一个指向对象的指针，`*E`就是一个`lvalue`，它指定了(designates)`E`所指向的对象。

{% alert danger no-icon %}

希望大家能够遵循规范，不要再将`lvalue`和`rvalue`称呼为「左值」和「右值」，并不是说这种称呼不好，而是有点望文生义的感觉，毕竟我们的目标是成为专业的程序开发人员。

{% endalert %}

`lvalue`是一个对象类型(object type)不是`void`的表达式，它可能指定了一个对象。如果`lvalue`在使用时并没有指定(designate)一个对象，这是一种未定义行为(undefined behavior)。当我们说一个对象具有特定类型时，类型被指向对象的`lvalue`所规定。

:question: 有哪些操作数（Operand）是可修改的`lvalue`?
{% alert success no-icon %}

- 没有数组类型(array type)的`lvalue`
- 没有不完整类型（incomplete type）的`lvalue`
- 没有常量限定符（`const`）类型的`lvalue`，如果对象是一个结构体(`structure`)或联合体(`union`)，则没有任何常量限定符类型的成员(通过递归方式查看的所有成员)

{% endalert %}

`lvalue`除了是 `sizeof` 操作符、`_Alignof` 操作符、一元取址及间接运算(Address and indirection operators)`&`操作符、`++`操作符、`—-`操作符、`.`操作符的左操作数(left operand)或赋值操作符`=`的操作数时，非数组类型的`lvalue`会被转换为存储在指定对象中的值，此时不再代表`lvalue`，这种行为称为「定位符值转换」(lvalue conversion)。

:question: `lvalue conversion`是如何转换的？哪些情况下是 UB（Undefined Behavior）的？
{% alert success no-icon %}

- 如果`lvalue`具有限定类型(qualified type)，则该值是`lvalue`类型的非限定版本；如果`lvalue`是原子类型(atomic type)，则该值时具有`lvalue`类型的非原子版本；否则，该值的类型与`lvalue`相同。
- 如果`lvalue`的类型是一种不完整类型并且不是数组类型，则当前操作属于为定义行为。
- 如果`lvalue`指定了一个自动存储期（automatic storage duration）的对象，该对象可以用寄存器存储类声明（从未使用过它的地址），并且该对象没有初始化（没有使用初始化方法声明，在使用之前没有对它进行赋值），那么当前操作是未定义行为。

{% endalert %}

`lvalue`除了是 `sizeof` 操作符、`_Alignof` 操作符、一元`&`操作符的操作数或者是使用字符串字面量(string literal)初始化数组的操作数，具有「数组类型」（array of type）的表达式会被转换为类型为「指向类型的指针」(pointer to type)的表达式，该表达式指向数组对象的初始元素，并且不是`lvalue`。如果数组对象具有寄存器存储类，则当前操作是未定义行为。

## 附录

[^1]: https://port70.net/~nsz/c/c11/n1570.html#6.3.2.1
