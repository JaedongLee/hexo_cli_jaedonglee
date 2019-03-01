---
title: Java-Note-Pattern-装饰器模式个人心得
date: 2018-12-27 11:43:16
tags: Pattern
catagory: Pattern
---
- 作用 : 装饰器模式属于`Structure Design Pattern`, 利用Java语言的多态性, 可以在运行时多层次的修改一个实例的特定方法, 并且该实例的其它部分不受影响
- 要点 :         
   1. 装饰器类扩展自被装饰的类(下文称为基类)的接口(下文称为接口), 修改类扩展自装饰器类, 这样的链式扩展保证了修改类可以利用其它修改类进行初始化, 从而为基类增加更多的功能
   2. 装饰器类中需要有接口的成员变量并被正常初始化, 以修改指定实例的方法并保证修改类在利用其它修改类进行初始化时, 增加的功能不会被被利用的修改类覆盖
- 例子 :
  - 接口及基类 :
  ```java
    interface Coffee {
        public void get();
    }

    class CoffeeImpl implements Coffee {
        @Override
        public void get() {
            System.out.println("get coffee");
        }
    }
  ```
  - 装饰器类 :
  ```java
    class CoffeeDecorator implements Coffee {
        private Coffee coffee;


        public CoffeeDecorator(Coffee coffee) {
            this.coffee = coffee;
        }

        @Override
        public void get() {
            coffee.get();
        }
    }
  ```
  - 装饰类 :
  ```java
  class MilkCoffee extends CoffeeDecorator {

    public MilkCoffee(Coffee coffee) {
        super(coffee);
    }

    @Override
    public void get() {
        System.out.println("add milk");
        super.get();
    }
  }

  class ChocolateCoffee extends CoffeeDecorator {
    public ChocolateCoffee(Coffee coffee) {
        super(coffee);
    }
    @Override
    public void get() {
        System.out.println("add chocolate");
        super.get();
    }
  }

  ```
  - 测试类 :
  ```java
  public class Test {
    public static void main(String[] args) {
        ChocolateCoffee coffee = new ChocolateCoffee(new MilkCoffee(new CoffeeImpl()));
        coffee.get();
    }
  }
  ```