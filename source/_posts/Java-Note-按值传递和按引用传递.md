---
title: Java-Note-按值传递和按引用传递
date: 2018-06-27 11:02:06
tags: 
category: Java
---
- 程序设计语言中有关参数传递给方法的两种方式
    - 按值传递(call by value)
    - 按引用传递(call by reference): 传递的是指针, 其指向的内存地址可以被改变
- 宗旨: Java的参数传递只有按值传递, 没有按引用传递
<!-- more -->
- 基本类型参数的传递方式: 按值传递
    - 例子: 不用过多介绍
- 引用类型参数的传递方式: 
    - 表面上是按引用传递
        - 例子: 
        ```
        public static void main(String[] args) {
            List<String> list1 = new ArrayList<>();
            list1.add("1");
            list1.add("2");
            list1.add("3");
            List<String> list2 = new ArrayList<>();
            list2.add("1");
            list2.add("2");
            list2.add("3");
            test1(list1);
            System.out.println("test1 outside: " + list1);
            test2(list2);
            System.out.println("test2 outside: " + list2);
        }

        private static void test1(List<String> list) {
            list.remove(1);
            System.out.println("test2 inside: " + list);
        }
        ```
        ```
        Output:
            test1 inside: [1, 3]`
            test1 outside: [1, 3]
        ```
        在`test1`中, list的值被改变, 看起来好像是按引用传递
    - 实际上还是按值传递, 这个值是引用的一个拷贝, 改变这个值不改变原引用指向的内存地址 指向的还是同一个内存地址
        - 例子:
        ```
        private static void test2(List<String> list) {
            List<String> tmp = new ArrayList<>();
            tmp.add("4");
            tmp.add("5");
            tmp.add("6");
            list = tmp;
            System.out.println("test1 inside: " + list);
        }
        ```
        ```
        Output:
            test2 inside: [4, 5, 6]
            test2 outside: [1, 2, 3]
        ```
        在`test2`中, list的值被改变, 但在`main`中, list2的值缺没有被改变, 这正好符合上面的解释