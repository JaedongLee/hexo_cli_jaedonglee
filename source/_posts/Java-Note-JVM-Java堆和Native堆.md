---
title: Java-Note-JVM-Java堆和Native堆
date: 2018-05-10 21:19:57
tags: JVM
category: Java
---
- Native堆属于非Java堆
    - 引入原因: JVM与外界通讯, 把Java堆中的内容传输到外界, 需要把Java堆复制到非Java堆, 如果使用Native堆并在Java堆中保存其引用, 可以避免内容在Java堆和非Java堆之间的copy
    - 注意
        - 非Java堆得回收不受Java young gc的影响, 需要手工回收. 大量使用丢失Java自动垃圾回收的特点
        - 一般使用费Java堆进行和外界的通讯, 并且作为缓存使用. 如DirectByteBuffer