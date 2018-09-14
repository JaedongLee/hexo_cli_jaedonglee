---
title: Java-Note-JVN-Could-not-reserve-enough-space-for-heap
date: 2018-05-15 10:35:50
tags: JVM
category: Java
---
- 问题: Could not reserve enough space for object heap
- 答案: 系统默认分配的堆内存不够, 可在catalina.sh开头添加: JAVA_OPTS="-Xmx1024m". 其中1024可以更改
<!-- more -->
