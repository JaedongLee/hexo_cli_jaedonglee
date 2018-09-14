---
title: Java-Note-foreach无法改变原数组的元素
date: 2018-03-21 16:43:37
tags:
category: Java
---
- `foreach`无法改变源数组的元素
    - reason: 赋值赋予的是在 foreach语句中创建的中间变量, 而不是源数组的元素, 如下面的代码所示:<!-- more -->
    ```
    for(int element:array1)//question：为什么用Foreach无法给数组赋值。
        {
                            element =  r.nextInt(101)+1;
        System.out.println(element);

        }//虽然可以输出十个随机数，但并没有把十个随机数赋给array1数组。
        这其中的执行过程是这样的
        element = array[i]
        element = 随机数
        syso(element)
        element是个基本数据类型，他不指向数组元素的地址，他只代表数字他自己
    ```