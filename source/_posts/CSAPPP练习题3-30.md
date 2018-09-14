---
title: CSAPPP练习题3-30
date: 2017-11-05 14:47:56
tags:
category: CSAPP
---
- 题目:       
    下面的C函数忽略了switch语句的主体. 在C代码中, 情况标号是不连续的, 而有些情况有多个标号.      
    <!-- more -->
    ``` 
    void switch2(long x, long *dest) {
        long val = 0;
        switch (x) {
            ... //Body of switch statement omitted
        }
        *dest = val;
    }
    ```
    在编译该函数时, GCC为程序的初始部分生成了以下代码. 变量x在寄存器%rdi中:
    ```
    // void switch2(long x, long *dest)
    // x in %rdi
    1   switch2:
    2       addq    $1, %rdi
    3       cmpq    $8, %rdi
    4       ja      .L2
    5       jmp     *.L4(, %rdi, 8)
    ```
    为跳转表生成以下代码(跳转表声明):
    ```
    1   .L4:
    2       .quad   .L9
    3       .quad   .L5
    4       .quad   .L6
    5       .quad   .L7
    6       .quad   .L2
    7       .quad   .L7
    8       .quad   .L8
    9       .quad   .L2
    10      .quad   .L5
    ```
    根据上述信息回答下列问题:       
    A. switch语句类情况标号的值分别是多少?        
    B. C代码中哪些情况有多个标号?       
- 解析:          
    - switch代码标号编译原则: 对C语言中的编号进行初始化, 使其从0开始     
    - 跳转表声明表示原则: 从原情况编号初始值开始往下排, 每行 +1, 右侧的字符串表示情况值.
    - 解析: 从初始部分汇编代码的第2行可以看出: GCC为了使编号初始值为0, 对原初始值 + 1, 因此原初始值为 -1.      
    从初始部分汇编代码第3/4行看出: 当初始值 >8 时, 会跳转到默认情况.L2, 因此情况编号最多到7.       
    根据跳转声明表示原则可排出各情况标号的值, 如下:
        - -1    .L9
        - 0     .L5
        - 1     .L6
        - 2     .L7
        - 3     .L2
        - 4     .L7
        - 5     .L8
        - 6     .L2
        - 7     .L5
- 答案:
    1. 如上列所示
    1. 通过上列可以看出, .L2/.L7/.L5有多个标号