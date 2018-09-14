---
title: Ant-Note-编译多个源并且在其中排除一部分文件
date: 2018-08-27 19:34:35
tags:
category: Ant
---
在开发过程中需要用到apache ant来编译源码, 在使用过程中遇到一个比较特殊的问题: 需要编译多个源目录并且在其中要排除掉部分文件. 编译多个源需要用到`javac`标签下面的`src`标签, 但如果使用两个`src`标签如:
```
<javac ...>
    <src path="src1">
    <src path="src2">
</javac>
```
则在下面加入`exclude`标签时, 不会将`exclude`下的文件排除在外, 如:
```
<javac ...>
    <src path="src1">
    <src path="src2">
    <exclude name="package/dir/srccode.java">
</javac>
```
由于`exclude`使用的是相对路径, 原因可能是因为多路径模式下无法判断该`exclude`标签属于哪个`src`, 有懂的兄弟请指出
为了`exclude`有效, 应该使用以下结构:
```
<javac ...>
    <src path="src">
    <include name="src1">
    <include name="src2">
    <exclude name="package/dir/code.java">
</javac>`
```
用一个更大的路径作为主src, 而将需要编译的src放到include里面, 这样exclude就是单个src下面的相对路径了, 经测试, 编译成功