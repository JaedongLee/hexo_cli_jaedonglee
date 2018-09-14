---
title: Java-Note-Spring-Autowired
date: 2018-04-25 17:28:18
tags: Spring
category: Java
---
- @Autowired: 可以将指定bean自动注入, 以避免使用`<bean>`下面的`<preporty>`来设置
- 使用@Autowired需要在xml中配置`<context:annotation-config>` 标签
    - `<context:annotation-config>` is used to activate annotations in beans already registered in the application context (no matter if they were defined with XML or by package scanning). and `<context:component-scan>` can also do what `<context:annotation-config>` does but `<context:component-scan>` also scans packages to find and register beans within the application context.
<!-- more -->
