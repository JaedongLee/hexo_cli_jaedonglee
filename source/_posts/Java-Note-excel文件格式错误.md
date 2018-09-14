---
title: Java-Note-excel文件格式错误
date: 2018-07-09 09:25:01
tags: Excel
category: Java
---
- 问题: 使用`workbook.write(outputStream)`下载的xsl文件报错: 文件格式无效
- 原因: 在设置response的header时, 把`Content-Length`设置为了初始文件的大小, 实际上在对初始文件进行操作后, 其大小发生了改变, 而且使用`workbook.write(outputStream)`进行下载不需要设置`Content-Length`
<!-- more -->
