---
title: Http-Note-ajax跨域请求导致sesion错误
date: 2018-03-29 10:12:37
tags:
category: Http
---
- 出于安全原因，浏览器限制从脚本内发起的跨源HTTP请求或返回结果被浏览器拦截, 因此, 在Ajax跨域请求时, 建立的sessionId不会被浏览器保存下来, 这样每次请求都会被当做一个新的客户端. 此时需要使用`CORS(跨域请求机制)`, 跨域资源共享（ CORS ）机制允许 Web 应用服务器进行跨域访问控制，从而使跨域数据传输得以安全进行.<!-- more -->
    - 具体方案: 
        1. 在服务器端增加过滤器, 在返回的`responseHeader`中添加以下内容:
        ```
        response.setHeader("Access-Control-Allow-Origin", request.getHeader("Origin"));
        response.setHeader("Access-Control-Max-Age", "3600");
        response.setHeader("Access-Control-Allow-Headers", "Origin, No-Cache, X-Requested-With, " +
                "If-Modified-Since, Pragma, Last-Modified, Cache-Control, Expires, Content-Type, X-E4M-With," +
                "userId,token,Access-Control-Allow-Headers, accept, Cookie");
        response.setHeader("Access-Control-Allow-Methods", "GET, HEAD, POST, PUT, DELETE, TRACE, OPTIONS, PATCH");
        response.setHeader("Access-Control-Allow-Credentials", "true");
        response.setHeader("XDomainRequestAllowed","1");
        ```
        2. 在前端使用`ajax`发起跨域请求时, 在`ajax`代码中设置`crossDomain`为`true`, `xhrFields`中的`withCredentials`为`true`, 示例代码如下:
        ```
        xhrFields: {
            withCredentials: true
        },
        crossDomain: true
        ```
    - **注意**: 所有的`ajax`请求函数中都要添加`2`中的代码