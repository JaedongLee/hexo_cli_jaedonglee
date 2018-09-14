---
title: Java-Note-Protected修饰符深入了解
date: 2018-05-17 11:38:24
tags: Protected
category: Java
---
- 在`Thinking in Java`中, protected的定义是: protected必须有继承关系才能访问. 但这句话到底是什么意思呢? 
- 首先弄清楚继承protected和访问protected的范围的区别
    - 继承protected: 可以在任意位置(包内或包外)继承基类的protected方法
    - 访问protected: 可以在:
    ```
    package baseClassInvokeTest;

    /**
    * @PackageName: baseClassInvokeTest
    * @Auther: zoutairan
    * @Description:
    * @Date: Created in 11:19 2018/5/17
    * @Modified By:
    */
    public class Base {
        protected void baseTest() {
            System.out.println("base");
        }
    }

    ```
    - 同一个包内任何类访问基类的protected方法
    ```
    package baseClassInvokeTest;

    import baseClassInvokeTest.Base;

    /**
    * @PackageName: baseClassInvokeTest
    * @Auther: zoutairan
    * @Description:
    * @Date: Created in 11:20 2018/5/17
    * @Modified By:
    */
    public class Test {
        public static void main(String[] args) {
            Base base = new Base();
            base.baseTest();
        }
    }

    ```
    - 不同包中子类可通过子类对象访问基类的protected方法
    ```
    import baseClassInvokeTest.Base;

    /**
    * @PackageName: baseClassInvokeTest
    * @Auther: zoutairan
    * @Description:
    * @Date: Created in 11:19 2018/5/17
    * @Modified By:
    */
    public class Sub extends Base {

        public void test() {
            Sub sub = new Sub();
            sub.baseTest();

        }
    }
    ```
    - 不同包中子类`不可以`创建基类对象访问基类的protected方法
    - 不同包中子类`不可以`创建另一个子类对象访问基类的protected方法
    - protected类的构造器默认也是protected的
- 总结: 只能在包内或者该导出类访问基类的protected方法