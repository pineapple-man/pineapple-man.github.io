---
title: LIST_HEAD
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: left
thumbnailImage:
categories: [Kernel, infrastructure]
tags: Kernel
keywords: [kernel, list_head]
excerpt: 本文主要记录在 Linux 中的 LIST_HEAD 基础设施
---

## 概述

这里假定读者已经具备关于链表（Linked List）的基本背景知识，若不太确定可以自行通过查看[这部分资料](https://www.andrew.cmu.edu/course/15-121/lectures/Linked%20Lists/linked%20lists.html)进行复习。

<!-- prettier-ignore-start -->
{% codeblock "/include/linux/types.h"  lang:C "https://elixir.bootlin.com/linux/v5.15/source/include/linux/types.h#L178" source_code %}
struct list_head {
    struct list_head *next, *prev;
};
{% endcodeblock %}
<!-- prettier-ignore-end -->

<!-- prettier-ignore-start -->
{% codeblock  "/tools/include/linux/list.h" lang:c  "https://elixir.bootlin.com/linux/v5.15/source/tools/include/linux/list.h#L48" list_add %}
/*
 * Insert a new entry between two known consecutive entries.
 *
 * This is only for internal list manipulation where we know
 * the prev/next entries already!
 */
#ifndef CONFIG_DEBUG_LIST
static inline void __list_add(struct list_head *new,
			      struct list_head *prev,
			      struct list_head *next)
{
	next->prev = new;
	new->next = next;
	new->prev = prev;
	prev->next = new;
}
#else
extern void __list_add(struct list_head *new,
			      struct list_head *prev,
			      struct list_head *next);
#endif

/**
 * list_add - add a new entry
 * @new: new entry to be added
 * @head: list head to add it after
 *
 * Insert a new entry after the specified head.
 * This is good for implementing stacks.
 */
static inline void list_add(struct list_head *new, struct list_head *head)
{
	__list_add(new, head, head->next);
}
{% endcodeblock %}

<!-- prettier-ignore-end -->

## list_sort

<!-- prettier-ignore-start -->
{% codeblock "linux/lib/list_sort.c" lang:c  https://github.com/torvalds/linux/blob/master/lib/list_sort.c list_sort.c%}

{% endcodeblock %}
<!-- prettier-ignore-end -->

## `container_of`

## 附录

[你所不知道的 C 語言: linked list 和非連續記憶體操作](https://www.youtube.com/watch?v=pTcRq__iKzI)
[The Linux Kernel API](https://www.kernel.org/doc/html/latest/core-api/kernel-api.html)
[Merge sort](https://en.wikipedia.org/wiki/Merge_sort)
[linked-list-goog-taste](https://github.com/felipec/linked-list-good-taste)
