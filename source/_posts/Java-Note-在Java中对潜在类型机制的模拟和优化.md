---
title: Java-Note-Java中潜在类型机制的实现和优化
date: 2019-01-04 17:09:43
tags: 
category: Java
---
- 潜在类型机制
  - 介绍: 具有潜在类型机制的语言只要求实现某个方法子集, 而不是某个特定的接口, 这样可以使代码更加泛化. 潜在类型机制不关心使用的类型是否来自同一个接口, 只要该些类型具有相同的方法.
- Java中潜在类型机制的简单模拟
  - 方法: 首先想到的就是利用**反射**来模拟
  - 例子:
  ```Java
  class Clazz1 {
      public void f() {
          System.out.println("Class1 f()");
      }

      public void g() {
          System.out.println("Class1 g()");
      }
  }

  class Clazz2 {
      public void f() {
          System.out.println("Class2 f()");
      }

      public void g() {
          System.out.println("Class2 g()");
      }
  }

  class ReflectTest {
      public static void perform(Object object) {
          Class clazz = object.getClass();
          try {
              Method method = clazz.getMethod("f");
              method.invoke(object);
          } catch (NoSuchMethodException e) {
              e.printStackTrace();
          } catch (IllegalAccessException e) {
              e.printStackTrace();
          } catch (InvocationTargetException e) {
              e.printStackTrace();
          }
          try {
              Method method = clazz.getMethod("g");
              method.invoke(object);
          } catch (NoSuchMethodException e) {
              e.printStackTrace();
          } catch (IllegalAccessException e) {
              e.printStackTrace();
          } catch (InvocationTargetException e) {
              e.printStackTrace();
          }
      }
  }

  public class Test2 {
      public static void main(String[] args) {
          ReflectTest.perform(new Clazz1());
          ReflectTest.perform(new Clazz2());
      }
  }
  ```
- 简单模拟的缺陷: 反射提供了一种模拟潜在类型机制的方法, 但是它将所有的类型检查都转移到了运行时, 因此无法进行编译期类型检查. 需要进行优化
- 对简单模拟的优化
  - 潜在类型机制的实质: 其创建了一个包含所需方法的隐式接口
  - 优化方法: 通过手工编写必须的接口(注意: 这里的接口与前文中的潜在类型机制不需要实现特定接口中的接口是不同的, 这里的接口是不改变已存在的拥有相同方法的类的代码的), 就可以实现潜在类型机制, 而这样不会用到反射, 从而可以进行编译期类型检查
  - 例子:
  ```Java
  package test;

  interface AdapterInterface {
      void f();

      void g();
  }

  class Clazz1 {
      void f() {
          System.out.println("Clazz1 f()");
      }

      void g() {
          System.out.println("Clazz1 g()");
      }
  }

  class Clazz2 {
      void f() {
          System.out.println("Clazz2 f()");
      }

      void g() {
          System.out.println("Clazz2 g()");
      }
  }

  class ClassAdapter1 implements AdapterInterface {
      private Clazz1 class1;

      public ClassAdapter1(Clazz1 class1) {
          this.class1 = class1;
      }

      @Override
      public void f() {
          class1.f();
      }

      @Override
      public void g() {
          class1.g();
      }
  }

  class ClassAdapter2 implements AdapterInterface {
      private Clazz2 class2;

      public ClassAdapter2(Clazz2 class2) {
          this.class2 = class2;
      }

      @Override
      public void f() {
          class2.f();
      }

      @Override
      public void g() {
          class2.g();
      }
  }

  class Performer {
      public static void perform(AdapterInterface adapterInterface) {
          adapterInterface.f();
          adapterInterface.g();
      }
  }

  public class Test {
      public static void main(String[] args) {
          Performer.perform(new ClassAdapter1(new Clazz1()));
          Performer.perform(new ClassAdapter2(new Clazz2()));
      }
  }
  ```