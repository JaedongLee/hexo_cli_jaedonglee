---
title: Java-Note-JVM-内存模型
date: 2018-03-30 11:51:00
tags:
category: Java
---
- 堆(`Heap`) [线程共享]
    - 作用: 用来存放各种类的实例对象
    - 分类目的: 使JVM能够更好的管理堆内存中的对象, 包括内存的分配和回收
    - 分类: <!-- more -->
        - 新生代(`Young`)
            - 规则:
                - `JVM`每次只会使用Edem和其中一块`Survivor`区域来为对象服务. 因此无论什么时候, 总有一块`Survivor`区域是空闲的.
                - 当对象在`Eden`和`From Surivor`区出生后, 如果对象还存活, 并且能够被`To Surivor`所容纳, 则使用复制算法将这些仍然存活的对象复制到其中, 清理`Eden`和`From Surivor`, 并将存活的对象的年龄`+1`. 当一个对象的年龄达到某个值(默认为`15`)时, 这些对象将会变成老年代
                - 对于一些较大的对象(即要分配一块较大的连续内存空间)则是直接进入到老年代
                - 在`JVM`进行`Minor GC`之前, 会检查`Old`的最大连续可用空间是否`Young`所有对象的总空间
                    - 大于: `Minor GC`可以确保是安全的. 
                    - 不大于: `JVM`会查看`HandlePromotionFailure`设置值是否允许担保失败.
                        - 允许: 检查`Old`最大连续空间是否大于历次晋升老年代的对象的平均大小
                            - 大于: 尝试进行一次`Minor GC`, 尽管有风险
                                - 风险: 当`Young`中的存活对象特别多时, `Minor GC`可能失败, 此时还需要进行`Full GC`
                            - 小于: 进行`Full GC`
                        - 不允许: 进行`Full GC`
            - GC方式:
                - 使用垃圾回收方式是: `Minor GC`, 所采用的算法是复制算法
            - 分类: 
                - `Eden`
                - `From Surivor`
                - `To Surivor`
        - 老年代(`Old`)
- 虚拟区栈(VM Stack) [线程私有]
    - 作用: 虚拟机栈的元素是栈帧, 方法执行时创建一个栈帧, 用来存储局部变量/操作数栈/动态链接/方法出口等信息. 每一个方法一个栈帧
- 本地方法栈(Native Method Stack) [线程私有]
    - `JVM`执行`Native`方法时使用, 不同的虚拟机有不同的方法, `HotSpot`虚拟机的本地方法栈和虚拟机栈合二为一.
- 方法区(Method Area) [线程共享]
    - 作用: 存储已经被`JVM`加载的类的类信息/常量/静态变量/即时编译后代码等数据.
    - 分类:
        - 常量池
            - 作用: 又称运行时常量池, 是方法区的一部分. 用于存放编译器生成的各种字面量和符号引用. 此外, 运行期间新的常量也会放入常量池中. 常见运行时常量池是通过`String`类的`intern()`方法
                - 字面量: 如文本字符串/final的常量值
                - 符号引用: 编译语言层面的概念, 包含:
                    - 类和接口的全限定名
                    - 字段的名称和描述符
                    - 方法的名称和描述符
- 程序计数器(Program Counter Register) [线程私有]
    - 作用: 可看做当前线程所执行字节码行号的指示器. 每个线程都有一个独立的程序计数器, 各个线程之间计数器互不影响, 独立存储. 
    - 疑问: `如果执行的是Java方法, 那么记录的是正在执行的虚拟机字节码指令的地址, 如果是native方法, 计数器的值为空(undefined).`, 如果native记录的地址的是`undefined`, 那么如何找到这个`Native`方法并执行呢?