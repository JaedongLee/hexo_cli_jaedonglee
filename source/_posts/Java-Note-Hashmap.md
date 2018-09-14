---
title: Java-Note-Hashmap
date: 2018-04-01 13:23:20
tags:
category: Java
---
- 摘要: hashmap是Java程序员使用频率最高的用于映射的数据类型
- 内部实现
    - 存储结构-字段<!-- more -->
        - 从结构上来讲, hashmap是数组+链表+红黑树实现的
        - 底层存储结构: 
            - Node[] table: 内部类, 实现了Map.Entry接口, 本质是一个映射(键值对)
            - 使用哈希表来存储. Java中hashmap采用链地址法.
                - 链地址法: 数组加链表的组合
                - hash值计算: 通过hashCode()获得值后, 通过高位运算和取模运算来产生hash值
                - hash碰撞: 两个hash值相等时,产生碰撞
                - 控制map使hash碰撞的几率小且`Node[] table`占用空间少: 好的hash算法和扩容机制
                - loadFactor: 负载因子, 
                    - 默认0.75, 是对空间和时间效率的平衡选择, 建议不要修改
                - threshold: 容纳的`key-value`对数量极限
                    - 是在此loadFactor和length(数组长度)对应下允许的最大元素数目, threshold = loadFactor * length
                    - 超过需扩容(resize())
                    - 扩容后的hashmap容量是之前容量的两倍
                - size: 实际大小, 当增加后的size大于threshold时, 进行resize()
                - length: table的长度
                    -  必须为2的n次方
                        -  原因: 取模和扩容时的优化
                - modcount: 结构发生变化的次数, key的value被覆盖不属于结构变化
                - 链表过长: 当链表过长时(大于8), 使用红黑树, 利用红黑树快速增删的特点提高性能
    - 功能实现-方法
        1. 确定哈希桶数组索引位置
        ```
        static final int hash(Object key) {
            int h;
            // h = key.hashCode(); 第一步 取hashCode值
            // h ^ (h >>> 16);  第二步 高位参与运算
            return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
        }
        static int indexFor(int h, int length) {  //jdk1.7的源码，jdk1.8没有这个方法，但是实现原理一样的
            return h & (length-1);  //第三步 取模运算
        }
        ```
        这里的hashCode()本质上就三步: 取key的hashCode值, 高位运算, 取模运算
        - 取模运算: 用h&(length - 1)来获得h的模, 因为length是2的n次方, 所以这种方法是可行的, 而且效率会更高
        2. 分析HashMap中的put方法
            - 执行过程:         
            ![执行过程](https://note.youdao.com/yws/public/resource/340ba176925ee287a13ec8321f3181f3/xmlnote/ABBDD93C9EED450AADA9E90BE576AAEB/17133)
            - 注意
                - 当key相同时, 覆盖发生在下面的代码块中:
                ```
                if (e != null) { // existing mapping for key
                    V oldValue = e.value;
                    if (!onlyIfAbsent || oldValue == null)
                        e.value = value;
                    afterNodeAccess(e);
                    return oldValue;
                }
                ```
                此时, e与p同时指向相同的那个Node, 改变e的value同时会改变Node中的value
        3. 扩容机制
            - 构造函数中并未初始化table, loadFactor
            - 在第一次调用put()方法时, 使用resize()对hashMap对象进行初始化
            - 扩容方法
                1. 初始化新的Entry[]数组
                2. 将原来的值拷贝进去
                    - 注意拷贝的过程中, 原来的Entry数组的对象引用将被释放, 且重新计算每个元素在数组中的位置(因为模的除数不同了或者说异或的(length-1)不同了)