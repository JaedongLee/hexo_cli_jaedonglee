---
title: Java-Note-改动文件内容后部署war包不起作用
date: 2018-03-26 10:19:41
tags:
category: Java
---
- problem: 一个`SSM`的小项目, 在改动`jdbc.properties`文件后不起作用, 将`jdbcUrl=jdbc\:mysql\://218.94.120.167:33002/jeecmsv8?characterEncoding\=utf8&allowMultiQueries\=true`改为`jdbcUrl=jdbc\:mysql\://localhost:3306/jeecmsv8?characterEncoding\=utf8&allowMultiQueries\=true`, 在`intellij IDEA`中运行成功, 但当部署到`Tomcat`的`webapps`路径下时日志打印出来依旧显示原来的`jdbcUrl`
- answer: 服务器中有两个相同版本的`Tomcat`程序, 一个是安装的, 一个是直接解压缩的, 当使用解压缩版的`Tomcat`启动时, 其加载的依然是安装版的`Tomcat`程序路径下的`webapps`, 因此使用的还是原来的应用<!-- more -->