---
title: XML-Note-相关知识
date: 2018-04-25 17:16:48
tags: 
category: XML
---
- XSD: XML Schema Defination, is a World Wide Web Consortium(W3C) recommendation that specifies how to formally describe the elements in an Extensible Markup Language(XML) document
- beans标签中的属性:
<!-- more -->
```
<beans xmlns = "http://www.springframework.org/schema/beans"
       xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context = "http://www.springframework.org/schema/context"
       xsi:schemaLocation = "
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context-3.0.xsd
">
```
- schemaLocation: The xsi:schemaLocation attribute locates schemas for elements and attributes that are in a specified namespace. Its value is a namespace URI followed by a relative or absolute URL where the schema for that namespace can be found. 引用的链接中的xsd文件是目前的xml文件中属性的框架, 如果有些标签不在schemaLocation中, 则spring会报错, 例如:`The matching wildcard is strict, but no declaration can be found for element 'context:annotation-config'.`
