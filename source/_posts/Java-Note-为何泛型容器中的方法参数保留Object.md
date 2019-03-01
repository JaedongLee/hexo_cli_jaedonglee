---
title: Java-Note-为何泛型容器中的方法参数保留Object
date: 2019-01-07 20:11:33
tags:
category: Java
---
今天在阅读的时候突然发现一个问题, 官方工具库里面的泛型容器有一部分方法并未使用泛型方法, 而是依旧沿用JDK1.5之前的`Object`, 例如: 
- `Map` 
  ```Java
    boolean containsKey(Object key);

    boolean containsValue(Object value);

    V get(Object key);

    V remove(Object key);

    boolean equals(Object o);

    ...
  ```
  - `List`
  ```Java
    boolean contains(Object o);

    boolean remove(Object o);

    boolean equals(Object o);

    ...
  ```
  - 原因
    1. 这些含`Object`类型参数的方法都有一个共同特点, 即需要与容器内的Key或者Value进行比较, 而在Java的规范中, 比较是不需要类型完全相同的, 例如`Number`类型的实例可以和`Long`类型的实例相比较
    2. 在Java中比较方法需要重写`Object`中的`equals(Object o)`, 所以用到`equals(Object o)`的方法的参数自然用`Object`而不是泛型
  - 你们以为这就完了吗?
  - ...
  - ...
  - ...
  - 反驳: 使用`Object`可能造成的错误比原因1中的好处要大得多, 而且涉及到`equals`完全可以重新创建一个方法例如`getByEquals(Object o)`与`get(K k)`一起使用
  - 个人结论: 可能存在设计缺陷(不要打我...)
  - 佐证: 在比Java新的C#中, `Map`(`Dictonary`)的`get`方法参数是`(K k)`