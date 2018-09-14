---
title: 深入了解计算机系统CSAPPP练习题3.16
date: 2017-10-24 09:47:38
tags:
category: CSAPP
---
- exercise 3.16
    已知下列C代码
    ```
    void cond(long a, long *p) {
        if (p && a > *p) 
            *p = a;
    }
    ```
    GCC会产生下面的汇编代码   <!-- more -->
    ```
    cond:
        testq   %rsi, %rsi
        je      .L1
        cmpq    %rdi, (%rsi)
        jge     .L1
        movq    %rdi, (%rsi)
    .L1
        rep; ret
    ```
    - 疑惑: `if (p && a > *p)`为何会在汇编语言中产生`cmpq    %rdi, (%rsi)`? `p && a`在汇编中为何没有反映出来
    - 解答: 运算符的优先级在书中没有讲到, `>`的优先级大于`&&`的优先级, 因此上面的代码片段应理解为: `if (p && (a > *p))`, 其中`a > *p`在`cmpq    %rdi, (%rsi)`中反映出来. 而`p && ()`在汇编中等价于`testq   %rsi, %rsi      je  .L1 ...`, 即当`p`不为空且`a > *p`不为零时, 执行`cmpq    %rdi, (%rsi)`否则结束程序