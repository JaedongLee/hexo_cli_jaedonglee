---
title: Java-Note-Pattern-动态代理个人心得
date: 2018-12-27 16:58:34
tags: Pattern
category: Pattern
---
- 介绍: 通过实现`InvocationHandler`接口, 动态的生成代理类.
- 优点: 
  1. 无需重写要修改的方法, 在普通代理代码变得很复杂的情况下, 动态代理可以减少代码量.
  2. 通过动态代理可以实现AOP(面向切面编程)
- 缺点: 基类必须实现接口, 没有普通代理方便.
- 要点:
  1. 代理类中有被代理类的成员变量
  2. 代理类必须初始化被代理类的实例
  3. 代理类中的`invoke(Object proxy, Method method, Object[] args)`用来动态执行需要修改的方法以及在其之前的操作
  4. 要用`Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)`来生成代理类的实例
     1. 第二个参数`Class<?>[] interfaces`表明基类必须实现接口且可以由多个基类和其接口
- 例子:
  - 接口
    ```java
    interface Interface {
            void doSomething();

            void somethingElse(String arg);
    }
    ```
  - 基类
    ```java
    class RealObject implements Interface {
        @Override
        public void doSomething() {
            print("doSomething");
        }

        @Override
        public void somethingElse(String arg) {
            print("somethingElse "+arg);
    }
    }
    ```
  - 代理类
    ```java
    class SimpleProxy implements Interface {
        private Interface proxied;

        public SimpleProxy(Interface proxied) {
            this.proxied = proxied;
        }

        @Override
        public void doSomething() {
            print("SimpleProxy doSomething");
            proxied.doSomething();
        }

        @Override
        public void somethingElse(String arg) {
            print("SimpleProxy somethingElse" + arg);
            proxied.somethingElse(arg);
        }
    }
    ```
  - 测试类
    ```java
    public class SimpleProxyDemo {
        public static void consumer(Interface iface) {
            iface.doSomething();
            iface.somethingElse("bonobo");
        }
        public static void main(String[] args) {
            consumer(new RealObject());
            consumer(new SimpleProxy(new RealObject()));
        }
    }
    ```