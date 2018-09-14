---
title: Java-Note-引用
date: 2018-05-10 08:05:48
tags: 
category: Java
---
- 引用: 
    - C++中的引用: 与指针相比:
        1. 没有null 引用
        2. 引用必须有初值
        3. 使用引用要比使用指针效率高, 因为引用不用测试其有效性
        4. 指针可以重新赋值, 而引用总是指向它最初获得的对象
    - Java中的引用: 与C++中的引用相比, 不过是穿着引用外衣的指针而已
    - 例子:
    ```
    public class ValueTransmit {
        public static void main(String[] args) {
            ValueObject valueObject1 = new ValueObject();
            System.out.println("old preprocess: " + valueObject1.value);
            changeOldObject(valueObject1);
            System.out.println("old processed: " + valueObject1.value);
            ValueObject valueObject2 = new ValueObject();
            System.out.println("new preprocess: " + valueObject2.value);
            changeNewObject(valueObject2);
            System.out.println("new processed: " + valueObject2.value);
        }

        public static void changeOldObject(ValueObject valueObject) {
            valueObject.value = 5;
            System.out.println("old processing: " + valueObject.value);
        }

        public static void changeNewObject(ValueObject valueObject) {
            valueObject = new ValueObject();
            valueObject.value = 5;
            System.out.println("new processing: " + valueObject.value);
        }
    }

    class ValueObject {
        public int value;

        public ValueObject() {
            value = 1;
        }
    }/* Output:
    old preprocess: 1
    old processing: 5
    old processed: 5
    new preprocess: 1
    new processing: 5
    new processed: 1
    *///:~
    ```
    - 疑问: 为何把`valueObject`引用指向一个new `ValueObject`后, 处理的结果没有发生变化呢
    - 答案: 因为方法参数是将实际参数复制一份压入栈中, 而`valueObject`引用如果指向的是new `ValueObject`的话, `value`的改变发生在new `ValueObject`, 而不是old `ValueObject`