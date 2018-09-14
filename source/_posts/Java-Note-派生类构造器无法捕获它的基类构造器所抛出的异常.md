---
title: Java-Note-派生类构造器无法捕获它的基类构造器所抛出的异常
date: 2018-08-17 08:05:02
tags:
category: Java
---
- 原因: `When you throw an exception, the compiler creates no object. This is why a derived-class constructor cannot catch a base-class constructor exception: it can't "recover" from the exception failure, since there's no base-class sub-object.`