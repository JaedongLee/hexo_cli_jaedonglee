---
title: Java-Problem
date: 2018-01-25 11:06:18
tags:
category: Java
---
- springMVC中关于将requst中的参数传入Controller中不同的对象的问题
- 问题: 为何request中的复杂参数无法直接写在Controller的参数中, 而必须用一个新对象去接受
- 例子:
    request:<!-- more -->
    ```
    POST /order/test.do HTTP/1.1
    Host: localhost:8080
    Content-Type: application/x-www-form-urlencoded
    Cache-Control: no-cache
    Postman-Token: 5569ecd9-3f2a-dcb9-c835-7697cbe2c1dc

    sessionKey=123&buyVesselId=123&saleVesselId=123&buyUser=123&item%5B0%5D.barcode=123&item%5B0%5D.count=123&item%5B1%5D.barcode=123&item%5B1%5D.count=123
    ```
    Class ItemListForm:
    ```
    public class ItemListForm {
        private List<Item> item;

        public List<Item> getItem() {
            return item;
        }

        public void setItem(List<Item> item) {
            this.item = item;
        }
    }
    ```

    Controller:  (wrong)
    ```
    @RequestMapping(value = "/order/createOrder", produces = "application/json; charset=utf-8")
    @ResponseBody
    public List<Item> createOrder(Order order, `List<Item>` itemListForm) {
        return itemListForm;
    }
    ```

    Controller:  (right)
    ```
    @RequestMapping(value = "/order/createOrder", produces = "application/json; charset=utf-8")
    @ResponseBody
    public ItemListForm createOrder(Order order, `ItemListForm` itemListForm) {
        return itemListForm;
    }
    ```

- 寻找让方法的某个参数可以传入不同对象的方法

- vesselLossId 到底在哪里!!!!!!!
- java.lang.Exception 原理
- 抛出Exception和抛出其子Exception有什么区别
    - 补充: 抛出的内容相同
    - 猜想: 效率不同
- Class类中的reflectionData()方法何时才能返回null, 因为调用它的privateGetDeclaredMethods方法会判断是否为null
- Mybatis multiple result sets must use store procedure
- Java中如何根据MIME类型得到后缀名类型
- 在时间压力面前, 是选择不顾bug快速实现还是想全面在写
- 为何子类无法调用基类的protected方法