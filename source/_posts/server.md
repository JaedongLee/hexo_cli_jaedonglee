---
layout: host
title: host is not allowed to connect to this mysql server
date: 2017-10-24 09:53:59
tags:
category: Mysql
---
- 原因: 很明显云服务器入站流量和防火墙都没问题, 问题出在云服务器上的mysql数据库上
- 解决方法: <!-- more -->
    - 从要连接的主机登录本地的mysql(即云服务器上的mysql), 
    - 进入名称为"mysql"的数据库中, 找到表"user", 找到host字段值为"localhost"且User字段值为"root"的那一行, 
    - 将host字段的值改为"%",
    - 重新启动mysql server(可使用mysql notifier)
    - 尝试从远程连接mysql, 成功!