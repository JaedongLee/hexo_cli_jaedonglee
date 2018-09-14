---
title: Java-Note-Redis安装
date: 2018-03-28 08:05:48
tags: Redis
category: Java
---
- 在`redis`和`java`应用进行通讯的过程中, 可以把`redis`理解成一个需要连接的数据库, 因此, 可以在`java`代码中连接`redis`
- 安装(慕课网有相关教程)<!-- more -->
1. 安装`redis`: `redis`官方只有`linux`版本, 因此最好在云主机上安装, 在通过`ip`和`port`进行连接
    - 在安装完`redis`后, 记得修改`redis.conf`, 否则外网无法连接`redis`
2. 下载`jedis`的`jar`包: `jedis`是`redis`官方推荐的`java`客户端, 使用它可以在`java`语言中直接操纵`redis`