---
title: ArrayList 源码分析
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
date: 2021-10-30 19:44:30
thumbnailImage:
categories:
  - Java
tags: Java
keywords: ArrayList
excerpt: 本文主要讲解 JDK 中 ArrayList的源码
---

<!-- toc -->

## 概述

:sparkles:ArrayList 特点

- *ArrayList*实现了*List*接口，是顺序容器，即元素存放的数据与放进去的顺序相同，允许放入`null`元素，底层通过**数组实现**
- <font style="color:red;font-weight:bold">除该类未实现同步外，其余跟*Vector*大致相同</font>
- 每个*ArrayList*都有一个容量(capacity)，表示底层数组的实际大小，容器内存储元素的个数不能多于当前容量
- 当向容器中添加元素时，如果容量不足，容器会自动增大底层数组的大小
- `size(), isEmpty(), get(), set()`时间复杂度$O(1)$
- `add()`方法的时间开销跟插入位置有关
- `addAll()`方法的时间开销跟添加元素的个数成正比，其余方法大都是线性时间。

:notes:Java 泛型只是编译器提供的语法糖，所以这里的数组是一个 Object 数组，以便能够容纳任何类型的对象

![ArrayList_base](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/ArrayList_base.png)

:notes:为**追求效率**，<font style="color:red;font-weight:bold">ArrayList 没有实现同步(synchronized)</font>，如果需要多个线程并发访问，用户可以手动同步，也可使用 Vector 替代

## 底层数据结构

```java
/**
 * The array buffer into which the elements of the ArrayList are stored.
 * The capacity of the ArrayList is the length of this array buffer. Any
 * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
 * will be expanded to DEFAULT_CAPACITY when the first element is added.
 */
transient Object[] elementData; // non-private to simplify nested class access

/**
 * The size of the ArrayList (the number of elements it contains).
 *
 * @serial
 */
private int size;
```

## 构造函数

```java
/**
 * Constructs an empty list with the specified initial capacity.
 *
 * @param  initialCapacity  the initial capacity of the list
 * @throws IllegalArgumentException if the specified initial capacity
 *         is negative
 */
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}

/**
 * Constructs an empty list with an initial capacity of ten.
 */
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}

/**
 * Constructs a list containing the elements of the specified
 * collection, in the order they are returned by the collection's
 * iterator.
 *
 * @param c the collection whose elements are to be placed into this list
 * @throws NullPointerException if the specified collection is null
 */
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class)
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // replace with empty array.
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

## 自动扩容

:sparkles:`DEFAULT_CAPACITY`规定了底层数组最大容量为 10，当不断添加元素后，需要进行`ArrayList`的扩容机制

:sparkles:ArrayList 的扩容机制

- 每当向数组中添加元素时，都要去检查添加后元素的个数是否会超出当前数组的长度，如果超出，数组将会进行扩容，以满足添加数据的需求
- 数组扩容通过一个公开的方法`ensureCapacity(int minCapacity)`来实现

:notes:在实际添加大量元素前，也可以手动使用 ensureCapacity 来手动增加 ArrayList 实例的容量，以减少递增式再分配的数量

:sparkles:数组进行扩容时，会将老数组中的元素重新拷贝一份到新的数组中，每次数组容量的增长大约是其原容量的 1.5 倍

:older_man:ArrayList 使用技巧

- 数组扩容代价很高，在实际使用时，应该尽量避免数组容量的扩张
- 当我们可预知要保存的元素的多少时，要在构造 ArrayList 实例时，就指定其容量，以避免数组扩容的发生
- 根据实际需求，通过调用 ensureCapacity 方法来手动增加 ArrayList 实例的容量

```java
    public void ensureCapacity(int minCapacity) {
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;

        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }

    private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }

    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```

:notes:由于 Java GC 自动管理了内存，这里也就不需要考虑源数组释放的问题

![ArrayList_grow](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/ArrayList_grow.png)

## `add()`

:sparkles:*ArrayList*插入元素特点

- 跟 C++ 的*vector*不同，*ArrayList*没有`push_back()`方法，对应的方法是`add(E e)`，*ArrayList*也没有`insert()`方法，对应的方法是`add(int index, E e)`
- 这两个方法都是向容器中添加新元素，这可能会导致*capacity*不足，因此在添加元素之前，都需要进行剩余空间检查，如果需要则自动扩容。扩容操作最终是通过`grow()`方法完成的

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}

public void add(int index, E element) {
    rangeCheckForAdd(index);

    ensureCapacityInternal(size + 1);  // Increments modCount!!
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);
    elementData[index] = element;
    size++;
}
```

![ArrayList_add](https://cdn.jsdelivr.net/gh/pineapple-man/blogImage@main/image/ArrayList_add.png)

`add(int index, E e)`需要先对元素进行移动，然后完成插入操作，也就意味着该方法有着线性的时间复杂度

## `addAll()`

:sparkles:`addAll()`插入方法特点

- 根据位置不同也有两个版本，一个是在末尾添加的`addAll(Collection<? extends E> c)`方法，一个是从指定位置开始插入的`addAll(int index, Collection<? extends E> c)`方法
- 在插入之前也需要进行空间检查，如果需要则自动扩容
- 如果从指定位置插入，也会存在移动元素的情况
- `addAll()`的时间复杂度不仅跟插入元素的多少有关，也跟插入的位置相关

```java
public boolean addAll(Collection<? extends E> c) {
    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);  // Increments modCount
    System.arraycopy(a, 0, elementData, size, numNew);
    size += numNew;
    return numNew != 0;
}

public boolean addAll(int index, Collection<? extends E> c) {
    rangeCheckForAdd(index);

    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);  // Increments modCount

    int numMoved = size - index;
    if (numMoved > 0)
        System.arraycopy(elementData, index, elementData, index + numNew,
                         numMoved);

    System.arraycopy(a, 0, elementData, index, numNew);
    size += numNew;
    return numNew != 0;
}
```

## `set()`

```java
public E set(int index, E e) {
    rangeCheck(index);
    checkForComodification();
    E oldValue = ArrayList.this.elementData(offset + index);
    ArrayList.this.elementData[offset + index] = e;
    return oldValue;
}
```

:thinking:`ArrayList.this`是什么东西？

- 这是为了正确使用外部类加上的限定，以防止出现内部类的访问歧义

:sparkles:`类名.this`使用场景

- 当在一个类的内部类中，如果需要访问外部类的方法或者成员域的时候，如果使用 **this.成员域**(与 **内部类.this.成员域** 没有分别) 调用的显然是内部类的域 ， 如果我们想要访问外部类的域的时候，就要必须使用 `外部类.this.成员域`
- 加强使用语意，明确使用哪个类的属性或方法

## `get()`

```java
public E get(int index) {
    rangeCheck(index);

    return elementData(index);
}
```

## `remove()`

`remove()`方法有三个个版本，一个是`remove(int index)`删除指定位置的元素，`remove(Object o)`删除第一个满足`o.equals(elementData[index])`的元素和`removeAll(Collection<?> c)`删除所有

:sparkles:删除操作特点

- 删除操作是`add()`操作的逆过程，需要将删除点之后的元素向前移动一个位置

- 为了让 GC 起作用，必须显式的为最后一个位置赋`null`值

```java
public E remove(int index) {
    rangeCheck(index);
    modCount++;
    E oldValue = elementData(index);
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; //清除该位置的引用，让GC起作用
    return oldValue;
}
```

:notes:关于 Java GC 这里需要特别说明一下，**有了垃圾收集器并不意味着一定不会有内存泄漏**。对象能否被 GC 的依据是是否还有引用指向它，上面代码中如果不手动赋`null`值，除非对应的位置被其他元素覆盖，否则原来的对象就一直不会被回收

## `removeAll()`

```java
public boolean removeAll(Collection<?> c) {
    Objects.requireNonNull(c);
    return batchRemove(c, false);
}

private boolean batchRemove(Collection<?> c, boolean complement) {
    final Object[] elementData = this.elementData;
    int r = 0, w = 0; //read flag ,write flag
    boolean modified = false;
    try {
        for (; r < size; r++)
            if (c.contains(elementData[r]) == complement)
                elementData[w++] = elementData[r];
    } finally {
        // Preserve behavioral compatibility with AbstractCollection,
        // even if c.contains() throws.
        if (r != size) {
            System.arraycopy(elementData, r,
                             elementData, w,
                             size - r);
            w += size - r;
        }
        if (w != size) {
            // clear to let GC do its work
            for (int i = w; i < size; i++)
                elementData[i] = null;
            modCount += size - w;
            size = w;
            modified = true;
        }
    }
    return modified;
}
```

## `trimToSize()`

将底层数组的容量调整为当前列表保存的实际元素的大小的功能。它可以通过 trimToSize 方法来实现

```java
public void trimToSize() {
    modCount++;
    int oldCapacity = elementData.length;
    if (size < oldCapacity) {
        elementData = Arrays.copyOf(elementData, size);
    }
}
```

## Fail-Fast 机制

ArrayList 也采用了快速失败的机制，通过记录 modCount 参数来实现。在面对并发的修改时，迭代器很快就会完全失败，而不是冒着在将来某个不确定时间发生任意不确定行为的风险。

## 附录

[Collection - ArrayList 源码解析](https://www.pdai.tech/md/java/collection/java-collection-ArrayList.html)
