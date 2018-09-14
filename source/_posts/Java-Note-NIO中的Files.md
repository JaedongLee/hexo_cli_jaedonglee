---
title: Java-Note-NIO中的Files
date: 2018-04-23 19:04:29
tags: NIO
category: Java
---
- 介绍: `
This class consists exclusively of static methods that operate on files, directories, or other types of files.
In most cases, the methods defined here will delegate to the associated file system provider to perform the file operations.`
- 包含的方法:
    - Files.readAllBytes(): 根据路径读取所有byte, 实现中用到大量Channel
    - Files.write(): 向给定路径写入给定byte, StandardOpenOption defines the standard open options
