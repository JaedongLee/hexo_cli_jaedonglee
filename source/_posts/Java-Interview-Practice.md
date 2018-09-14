---
title: Java-Interview-Practice
date: 2018-03-15 09:14:14
tags:
category: Java
---
1.  -  question: 九种基本数据类型的大小, 以及他们的封装类
    - answer:
        1. byte/1/Byte
        2. short/2/Short
        3. char/2/Character
        3. int/4/Integer
        4. long/8/Long
        5. float/4/Float
        6. double/8/Double
        7. void/-/Void
        8. boolean/-/Boolean<!-- more -->
2.  - question: equals与==的区别
    - answer: 
        1. `equals`是对象的方法, `==`是操作符
        2. `equals`可以被改写, `==`不行
        3. `equals` can not compare with  `null`, `==` can
        4. `equals` is more flexible that it can be override, but `==` don't has this feature
        5. `equals` used to judge whether or not the two object is equal, if this method doesn't be overrided, this method will reference from `Object` class, so that the return is still the judgment of `==`
        6. `==` is used to judge whether or not two object's address are equal, which means that they refer to the same object
7.  - question: Object有哪些公用方法
    - answer: 
        1. equals()
        2. wait()
        3. toString()
        4. getClass()
        5. hasCode()
        6. notify()
        7. notifyAll()
8.  - question: Java的四种引用, 强弱软虚, 用到的场景.
    - answer: 
        1. 强引用: `Object object = new Object` / `String str = "hello"`
            - 强引用有引用变量访问时永远不会被垃圾回收, JVM宁愿抛出OutOfMemory错误也不会回收这种对象
        2. 软引用: 内存空间足够时, JVM不会回收它, 但当内存空间不足时, JVM会回收这些对象的内存
            - 软引用可以用来实现内存敏感的高速缓存, 如图片缓存/网页缓存等. 使用软引用可以防止内存泄漏, 增强程序的健壮性
            - SoftReference的特点是它的一个实例保存着对一个Java对象的软引用, 软引用的存在并不妨碍JVM对该对象的回收
            - 如果一个SoftReference引用了一个Java对象, 那么在这个对象被JVM回收之前, SoftReference都可以通过`get()`方法来获得Java对象的强引用
            - 例子:
                ```
                Object aRef = new Object();
                SoftReference aSoftRef = new SoftReference(aRef);
                ```
                然后, 结束这个强引用
                ```
                aRef = null
                ```
                之后, 这个对象成为了软引用. 如果JVM进行内存垃圾回收, 并不会因为有一个SoftReference对该对象的引用而保留该对象
            - 软引用的清理是JVM根据特定算法按照内存需求决定的, JVM会尽可能的优先回收长期闲置不用的软引用对象, 对那些刚刚创建或刚刚使用过的"新"软引用对象会被虚拟机尽可能保留.
            - 可以通过`MyObject anotherRef = (MyObject) aSoftRef.get()`可以获得对这个实例的强引用
        3. 弱引用: 弱引用也是用来描述非必需对象, 当JVM发起GC时, 弱引用一定会被回收, 不管内存是否充足. 在java中, 用`java.lang.ref.WeakReference`类来表示. 下面是使用示例:
            ```
            public class WeakReferenceDemo {
                public static void main(String[] args) {
                    WeakReference<People> reference = new WeakReference<People>(new People("zoutairan", 25));
                    System.out.println(reference.get());
                    System.gc();
                    System.out.println(reference.get());
                }
            }
            class People {
                public String name;
                public int age;

                public People(String name, int age) {
                    this.name = name;
                    this.age = age;
                }

                @Override
                public String toString() {
                    return "[name:" + name + ", age:" + age + "]";
                }
            }
            ```
            输出结果:
            ```
            [name:zoutairan, age:25]
            null
            ```
            如果对代码进行一点小小的更改:
            ```
            public class WeakReferenceDemo {
                public static void main(String[] args) {
                    People people = new People();
                    WeakReference<People> reference = new WeakReference<People>(people));
                    System.out.println(reference.get());
                    System.gc();
                    System.out.println(reference.get());
                }
            }
            class People {
                public String name;
                public int age;

                public People(String name, int age) {
                    this.name = name;
                    this.age = age;
                }

                @Override
                public String toString() {
                    return "[name:" + name + ", age:" + age + "]";
                }
            }
            ```
            输出结果:
            ```
            [name:zoutairan, age:25]
            [name:zoutairan, age:25]
            ```
        4. 虚引用: 如果一个对象跟虚引用关联, 则跟没有引用一样, 随时都可能被JVM回收
            - 注意: 虚引用必须和引用队列相关联, 当JVM回收一个对象时, 如果发现它还有虚引用, 则会把这个虚引用加入与之相关联的引用队列, 那么就可以在所引用的对象的内存被回收之前采取必要的行动
        - 当一个`SoftReference`所引用的对象被设置成`null`后, 再调用`System.gc()`并不会立刻销毁这个软引用, 而是要等JVM发起`minorGC`后再销毁这个引用变量.
1.  - question: Hashcode的作用
    - answer: 
        1. `hashCode`的存在主要是用于查找的快捷性, 如`HashMap`, `Hashtable`等, `hashCode`是用来在散列结构中确定对象的存储地址.
        2. 如果两个对象相同, 即使用`equals`进行比较时, 返回`true`. 它们的`hashCode`也相同.
        3. 如果对象的`equals`方法重写, 那么`hashCode()`方法也要重写, 而且`equals`中使用的对象要和`hashCode()`中的相同
        4. 两个对象的`hashCode`相同, 并不一定表示两个对象相同, 只能够说明这两个对象在散列存储结构中, 如`Hashtable`, 他们在同一个链表下.
5.  - question:  ArrayList、LinkedList、Vector的区别
    - answer: 
        1. `ArrayList`和`Vector`的区别:
            2. `ArrayList`不是线程安全的, `Vector`是线程安全的
            3. `ArrayList`在底层数组不够用时在原来的基础上扩展0.5倍, `Vector`是扩展一倍
        4. `ArrayList`和`LinkedList`的区别:
            5. `ArrayList`是实现了基于动态数组的数据结构, `LinkedList`是居于链表的数据结构
            6. 对于随机访问(`get()`/`set()`), `ArrayList`直接根据下标索引, 时间复杂度为`O(1)`, `LinkedList`需要一个个节点的索引, 时间复杂度为`O(n)`
            7. 对于增删操作, `ArrayList`需要调用`System.arraycopy`移动指定下标以后的所有数组, `LinkedList`主要耗时是先通过循环找到索引所在位置, 再进行插入或删除, 因此无法比较两者谁快谁慢
1.  - question: String，StringBuilder，StringBuffer的区别
    - answer: 
        1. 对象类型: `String`-字符串常量/`StringBuilder`-字符串变量/`StringBuffer`-字符串变量
        2. 线程是否安全: 不安全/不安全/安全
        3. 大致执行效率: 低/高/中
        4. 使用场景: 操作少, 数据少/单线程, 操作多, 数据多/多线程, 操作多, 数据多
        2. 对`String`改变值是一个不断创建新的对象并回收旧对象的过程, 而`StringBuilder`和`StringBuffer`则是直接在原对象上进行更改, 因此`String`是最慢的
1. - question: Map/Set/List/Queue/Stack的特点与用法
    - answer:
        - Map: 键值对, 键唯一不能重复, 一个键对一个值
            - HashMap: 无序
            - TreeMap: 有序
            - LinkedHashMap: 有序
            - 可以把Key和Value单独抽取出来
                - KeySet()把所以得keys抽取成一个Set.(为什么不抽取成Collection, 因为Set不可重复, Collection容许重复, 而Key不会重复)
        - Set: 不包含重复元素, Set中最多包含一个null元素, 只能用Iterator实现单项遍历
        - List: 有序的可重复集合, 可使用iterator实现单向遍历, 也可以使用Listiterator实现双向遍历
        - Queue: Queue遵循先进先出的原则, 使用时尽量不要使用add()和remove()而是使用offer()和poll()
        - Stack: Stack遵循后进先出原则, 继承自Vector, 它提供了通常的push和pop, 以及peek, empty方法
        - 用法:
            - 如果涉及堆栈，队列等操作，建议使用List 
            - 对于快速插入和删除元素的，建议使用LinkedList 
            - 如果需要快速随机访问元素的，建议使用ArrayList
1.  - question: HashTable和HashMap的区别详解
    - answer: 
        - HashMap: 
            1. 内部维护着一个存储数据的Entry数组, Entry数组本质上是一个单项链表
            2. HashMap的扩充会将现有的容积*2
            2. HashMap内部key的hash值的算法: 
                ```
                static final int hash(Object var0) {
                    int var1;
                    return var0 == null ? 0 : (var1 = var0.hashCode()) ^ var1 >>> 16;
                }
                ```
                把`hashCode()`得出的值和自身值的前16位进行运算, 这样可以让key本身参与运算, 从而使hash值的分布更加均匀
            3. HashMap取模是使用`(length - 1) & hash`的方式, 这样比直接取模效率更高, 但使用范围更窄, 因为其只适用于`length`是2的乘方
            4. HashMap是线程不安全的
            5. HashMap继承的是AbstractMap, 实现了Map, Cloneable, Serializable接口
        - HashTable:
            1. HashTable是线程安全的
            2. HashTable的key和value不允许为null
            3. HashTable继承的是Dictionary, 实现了Map, Cloneable, Serializable接口
            4. HashTable的hash值直接采用的`hashcode()`的值
            5. 扩容方式是 `old * 2 + 1`
1.  - question: HashMap和ConcurrentHashMap的区别
    - answer: 
        - hashmap是线程不安全的, concurrenthashmap是线程安全的
        - concurrenthashmap采用分段技术, 将数组分成多个segment, 每个segment都有自己的锁, 当对concurrenthashmap进行操作时, 只需要对数据所在的那个segment进行操作. concurrenthashmap让锁的粒度更加精细一些, 并发性能更好.
1.  - question:  TreeMap、HashMap、LindedHashMap的区别。
    - answer: 
        1. TreeMap: 
            1. 红黑树: 
                1. 所有节点左边的子节点/孙节点等都比父节点小, 右边的都大
                2. 旋转的是当前节点, 只会影响前后两层节点, 父节点会成为该节点的左/右子节点
        3. LinkedHashMap
            4. 单向链表: 节点中只有下一个节点的引用
            5. 双向链表: 节点中有上下两个节点的引用
            6. 实现方式: 在Entry中增加了before和after的Entry引用, 分别指向前一个和后一个放入的Entry
1.  - question:  try catch finally，try里有return，finally还执行么
    - answer: 会, 因为finally在return之前执行
    - 拓展: 当一个线程在执行 try 语句块或者 catch 语句块时被打断（interrupted）或者被终止（killed），与其相对应的 finally 语句块可能不会执行, System.exit (0) 不会执行

1.  - question: Excption与Error包结构。OOM你遇到过哪些情况，SOF你遇到过哪些情况。
    - answer: Throwable是 Java 语言中所有错误或异常的超类。 
        - Throwable包含了其线程创建时线程执行堆栈的快照，它提供了printStackTrace()等接口用于获取堆栈跟踪数据等信息。
        - RuntimeException: 指那些在JVM正常期间抛出的异常的超类, 编译器不会检查
        - Error: 也是Throwable的子类, 当资源不足、约束失败、或是其它程序无法继续运行的条件发生时，就产生错误
1.  - 封装，也就是把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。
    - 继承: 继承是指可以让某个类型的对象获得另一个类型的对象的属性的方法。
    - 多态: 就是指一个类实例的相同方法在不同情形有不同表现形式。
        - 多态存在的三个必要条件
            1. 要有继承；
            1. 要有重写；
            1. 父类引用指向子类对象。


1.  - Override和Overload的含义去区别
        - override: 父类与子类之间多态性的一种表现
        - overload: 一个类中多态性的表现
1.  - interface和abstract 的区别和相同点
1.
1.
1.
1.
1.
1.