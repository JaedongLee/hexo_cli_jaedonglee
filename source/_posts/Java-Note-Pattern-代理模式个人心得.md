---
title: Java-Note-Pattern-代理模式个人心得
date: 2018-12-27 16:27:49
tags: Pattern
category: Pattern
---
- 代理模式属于`Structure Design Pattern`, 可以在不改动被代理类指定方法的情况下修改它, 比如增加功能, 权限控制等
- 要点: 
  1. 代理类中需要有接口的成员变量并被正常初始化, 以修改指定实例的方法并保证修改类在利用其它修改类进行初始化时
- 例子:
    - 接口及基类:
    ```java
    interface Coffee {
        void get();
    }

    class CoffeeImpl implements Coffee {
        @Override
        public void get() {
            System.out.println("get coffee");
        }
    }
    ```
    - 代理类:
    ```java
    class CoffeeProxy implements Coffee {
        Coffee coffee;

        public CoffeeProxy(Coffee coffee) {
            this.coffee = coffee;
        }

        @Override
        public void get() {
            System.out.println("add water");
            coffee.get();
        }
    }
    ```
    - 测试类:
    ```java
    public class CoffeeProxyTest {
        public static void main(String[] args) {
            Coffee proxy = new CoffeeProxy(new CoffeeImpl());
            proxy.get();
        }
    }
    ```