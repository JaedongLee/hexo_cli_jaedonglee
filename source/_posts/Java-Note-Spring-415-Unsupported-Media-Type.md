---
title: Java-Note-Spring-415-Unsupported-Media-Type
date: 2018-05-11 14:43:24
tags: Spring
category: Java
---
- 用postman向服务端发送请求时报错, 使用的`content-type`是`application/json`: 
```
HTTP Status 415 – Unsupported Media Type
Type Status Report
Description The origin server is refusing to service the request because the payload is in a format not supported by this method on the target resource.
```
- 原因: 
    1. 在请求头中未加入`Content-Type:application/json`
    2. 如果排除`1`依然报错, 则是因为没有相应的处理json的包, 如`com.google.code.gson`/`com.fasterxml.jackson.core`. 如果没有这类包, 则依旧会报这个错误, 因为java和spring自己没有解析json的功能, 需要借助第三方包