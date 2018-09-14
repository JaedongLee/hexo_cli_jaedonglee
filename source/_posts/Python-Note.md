---
title: Python_Note
date: 2017-11-09 11:13:04
tags:
category: Python
---
- Python基础
    - Python命令行: 可以直接在命令行中编写函数, 这样可以在cmd中调用函数
    - 使用list和tuple<!-- more -->
        - list
            - 定义: Python内置的一种数据类型是列表: list. list是一种有序的集合, 可以随时添加和删除其中的元素
            - 用法
                - list[-1]: 使用-1做索引获取最后一个元素, 依此类瑞, 可以获得倒数第二个/第三个元素
                - list.append('ele'): 向list末尾追加元素
                - list.insert(num, 'ele'): 将ele插入指定位置num
                - list.pop(): 删除末尾元素
                - list.pop(num): 删除指定位置的元素
                - list元素可以是另一个list, 获取元素的方式是: list[i][j]
        <!-- more -->
        - tuple
            - 定义: 与list非常类似, 但是tuple一旦初始化就不能修改
            - 注意: 在定义只有一个元素的tuple时, 需用`t = (, 1)`而不是`t = (1)`, 因为`t = (1)`定义的不是tuple而是`1`这个数
            - tuple所谓的不变是指: tuple的每个元素, 指向永远不变
    - 条件判断
        - input()
            - 注意: input()返回的是str类型, 不能和整数直接进行比较, 必须先使用int()转换成整数
    - 循环
        - break
            - 提前结束所属的循环
        - continue
            - 跳过当前的这次循环, 直接开始下一次循环
- 函数
    - 函数的参数
        - 默认参数
            - <font color=red>默认参数设为不变变量的问题:</font>
                - 例子:       
                    为了保证默认参数不会在多次调用函数时改变内容, 我们可以使用None这个不变变量来实现:
                    ``` 
                    code 1:
                    
                    def add_end(L=None):
                        if L is None:
                            L = []
                        L.append('END')
                        return L
                    ```
                    现在, 无论调用多少次, 都不会有问题:
                    ```
                    code 2:

                    >>> add_end()
                    ['END']
                    >>> add_end()
                    ['END']
                    ```
                - 问题: 为何在`code 1`第三行中将`[]`赋值给`L`后下一次调用`add_end()`时`L`初始依然为`None`
                - 答案: 每调用一次`add_end()`, 变量`L`就会指向一次`None`所在的地址. as `L` intialized to be `None`, 语句`L = []`会使`L`指向一个存放`[]`的新的内存地址,
                而原来存放`None`的地址的内容没有改变, 因此在下一次调用`add_end()`时, L还是等于`None`所在地址的内容, 即`L = None`
                - 默认参数的本质: 
                    - 函数在定义时会确定默认参数指向的地址, 当函数在被调用时默认参数会指向该地址, 在函数中可以改变默认地址的指向, 也可以改变默认参数的指向地址的值(当默认地址设置的不为不变变量)
        - 可变参数
            - 方法: 在定义函数参数时在前面加`*`. 例:
                ```
                def calc(*number):
                    sum = 0
                    for n in numbers:
                        sum += n*n
                    return sum
                ```
                定义可变参数和定义一个`list`或`tuple`参数相比, 仅仅在参数前面加了一个`*`号. 在函数内部, 参数`number`接收到的是一个`tuple`, 因此, 函数代码完全不变. 但是, 调用该函数时, 可以传入任意个参数, 包括零个.
            - 问题: 如果已经有一个`list`或`tuple`, 要调用一个可变参数怎么办? 
            - 答案: 可以这样做:
                ```
                >>> nums = [1,2,3]
                >>> calc(*nums)
                ```
                `*nums`表示把`nums`这个`list`的所有元素作为可变参数传进去. 这种写法相当有用, 而且很常见
        - 关键字参数
            - 作用:           
                允许传入0个或任意个含参数名的参数, 这些关键字参数在函数内部自动组装为一个`dict`. 例:
                ```
                def person(name, age, **kw):
                    print('name:', name, 'age:', age, 'other:', kw)
                ```
                函数`person`除了必选参数`name`和`age`外, 还接受关键字参数`kw`. 在调用该函数时, 可以传入任意个数的关键字参数:
                ```
                >>> person('Bob', 35, city='Beijing')
                name: Bob age: 35 other: {'city': 'Beijing'}
                >>> person('Adam', 45, gender='M', job='Engineer')
                name: Bob age: 35 other: {'gender': 'M', 'job': 'Engineer'}
                ```
            - 问题: 如果已经有一个`dict`, 要调用一个可变参数怎么办? 
            - 答案: 可以这样做:
                ```
                >>> extra = {'city': 'Beijing', 'job': 'Engineer'}
                >>> person('jack', 24, **extra)
                name: jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}
                ```
        - 命名关键字参数
            - 如果要限制关键字参数的名字, 就可以用命名关键字参数, 例如, 只接收`city`和`job`作为关键字参数. 这种方式定义的函数如下:
                ```
                def person(name, age, *, city, job):
                    print(name, age, city, job)
                ```
                和关键字参数**kw不同, 命名关键字参数需要一个特殊分隔符`*`, `*`后面的参数被视为命名关键字参数.
                ```
                >>> person('Jack', 24, city='Beijing', job='Engineer')
                Jack 24 Beijing Engineer
                ```
                如果函数定义中已经有了一个可变参数, 后面跟着的命名关键字参数就不再需要一个特殊分隔符`*`了:
                ```
                def person(name, age, *args, city, job):
                    print(name, age, args, city, job)
                ```
                命名关键字参数必须传入参数名, 这和位置参数不同. 如果没有参入参数名, 调用将报错
    - 递归函数
        - 尾递归优化
            - 定义:　
                1. 一句话定义: 函数在最后一步只`return`函数本身的方法
                2. 尾递归优化是指在函数返回时, 调用函数本身, 并且, `return`语句不能包含表达式
            - 解释: 
                1. 函数调用会在内存中形成一个"调用记录", 又称"调用祯", 用来保存调用位置和内部变量等信息. 如果在函数A的内部动用函数B, 那么在A的调用记录上方, 还会形成一个B的调用记录.
                1. 由于尾递归是函数的最后一步操作, 因此与目前的环境(context)没有任何关联,调用位置/内部信息等信息都不会再用到了. 所以不需要保留外层函数的调用记录. 只要直接用内层函数的调用记录, 取代外层函数的调用记录就可以了.
                    - 以尾调用为例, 尾递归属于尾调用:
                        ```
                        function f() {
                            let m = 1;
                            let n = 2;
                            return g(m + n);
                        }
                        f()

                        //等同于
                        function f() {
                            return g(3);
                        }
                        f()

                        //等同于
                        g(3);
                        ```
                    上面代码中, 乳沟函数`g`不是尾调用, 函数f就需要保存内部变量`m`和`n`的值, `g`的调用位置等信息. 但由于调用`g`之后, 函数`f`就结束了, 所以执行到最后一步, 完全可以删除`f()`的调用记录, 值保留g(3)的调用记录
- 高级特性
    - 切片
        - 作用: 提取一个list/tuple中的一段数列
        - 例子:
            ```
            L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']
            >>> L[0:3]      //从索引`0`开始取, 直到索引`3`为止, 但不包括索引`3`
            >>> L[-3:-1]        //从索引`-3`开始, 直到索引`-1`为止, 但不包括索引`-1`. 记住倒数第一个元素的索引是`-1`
            >>> L[-3:]      //包括索引`-1`
            ```
            ```
            L = [0,1,2,...,99]
            >>> L[:10:2]      //前十个数, 每两个取一个
            >>> L[::5]      //所有数, 每5个取一个
            >>> L[:]        //获取所有数
            ```
        - 注意: 
            1. tuple也是一种list, 唯一区别是tuple不可变. 因此, tuple也可以用切片操作, 只是操作结果仍是字符串. 例:
                ```
                >>> (0, 1, 2, 3, 4, 5)[:3]
                (0, 1, 2)   
                ```
            2. 字符串`'xxx'`也可以看成是一种list, 每个元素就是一个字符. 因此, 字符串也可以用切片操作, 只是操作结果仍是字符串
                ```
                >>> 'ABCDEFG'[:3]
                'ABC'
                >>> 'ABCDEFG'[::2]
                'ACEG'
                ```
        - 练习
            - 题目: 利用切片操作，实现一个trim()函数，去除字符串首尾的空格，注意不要调用str的strip()方法
            - 答案:
                ```
                def trim(s):
                l = len(s)
                begin = 0
                end = l-1
                while begin < l:
                    if s[begin] == ' ':
                        begin = begin + 1
                    else:
                        break
                while end > 0:
                    if s[end] == ' ':
                        end = end - 1
                    else:
                        break
                s_slice = s[begin:end+1]
                print(s_slice)
                print(len(s_slice))
                ```
    - 迭代
        - 定义: 如果给定一个list或tuple, 我们可以通过`for`循环来遍历这个`list`或`tuple`, 这种遍历我们称为迭代(Iteration)
            - 只要作用于一个可迭代对象, `for`循环就可以正常运行, 而我们不太关心该对象是list还是其它数据类型.
        - 用法:
            - 可用于没有下标的数据类型, 比如`dict`就可以迭代:
                ```
                >>> d = {'a': 1, 'b': 2, 'c': 3}
                >>> for key in d:
                ...     print(key)
                ...
                a
                c
                b
                ```
                PS: 由于`dict`的存储不是按照`list`的方式顺序排列, 所有, 迭代出的结果顺序很可能不一样
                - 默认情况下，dict迭代的是key。如果要迭代value，可以用for value in d.values()，如果要同时迭代key和value，可以用for k, v in d.items()。
            - 字符串也是可迭代对象, 因此, 也可以作用于`for`循环
        - 问题
            - 如何判断一个对象是可迭代对象?
                - 解答: 通过collections模块的Iterable类型判断:
                    ```
                    >>> from collections import Iterable
                    >>> isinstance('abc', Iterable)     #_str是否可迭代_
                    True
                    >>> isinstance([1,2,3], Iterable)     #_list是否可迭代_
                    True
                    >>> isinstance(123, Iterable)     #_整数是否可迭代_
                    False
                    ```
            - 如果要对list实现类似Java那样的下标循环怎么办?
                - 解答: Python内置的`enumerate`函数可以把一个list变成使用索引-元素对, 这样就可以在`for`循环中同时迭代索引和元素本身:
                    ```
                    >>> for i, value in enumerate('A', 'B', 'C']):
                    ...     print(i, value)
                    ...
                    0 A
                    1 B
                    2 C
                    ```
                上面的`for`循环里, 同时引进了两个变量, 在Python里是很常见的, 比如下面的代码:
                    ```
                    >>> for x, y in [(1, 1), (2, 4), (3, 9)]:
                    ...     print(x,y)
                    ... 
                    1 1 
                    2 4
                    3 9
                    ```
        - 练习
            - 题目: 请使用迭代查找一个list中最小和最大值, 并返回一个tuple
            - 答案: 
                ```
                def findMinAndMax(L):
                max = L[0]
                min = L[0]
                for i, value in enumerate(L):
                    if max < L[i]:
                        max = L[i]
                    if min > L[i]:
                        min = L[i]
                print(max)
                print(min)
                return (max, min)
                ```
    - 列表生成式
        - 定义: 列表生成式即List Comprehensions, 是Python内置的非常简单却强大的可以用来创建list的生成式.
        - 用法
            - 举例: 如果要生成[1*1, 2*2, 3*3,..., 10*10]怎么做?
                - 方法一: 循环
                    ```
                    >>> L = []
                    >>> for x in range(1, 11):
                    ...    L.append(x * x)
                    ...
                    >>> L
                    [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
                    ```
                - 方法二: 循环太繁琐, 而列表生成式则可以用一行语句代替循环生成上面的list:
                    ```
                    >>> [x * x for x in range(1, 11)]
                    [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
                    ```
                    - for循环后面还可以加上if判断, 这样我们就可以筛选出仅偶数的平方:
                        ```
                        >>> [x * x for x in range(1, 11) if x % 2 == 0]
                        [4, 16, 36, 64, 100]
                        ```
                    - 还可以使用两层循环, 可以生成全排列:
                        ```
                        >>> [m + n for m in 'ABC' for n in 'XYZ']
                        ['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
                        ```
                        三层及以上的循环就很少用到了
        - `for`循环其实可以同时使用两个甚至多个变量, ==`dict`的`item()`可以同时迭代key和value==
            ```
            >>> d = ['x': 'A', 'y': 'B', 'z': 'C']
            >>> for k, v in d.items():
            ...     print(k, '=', v)
            ...
            y = B
            x = A
            z = C
            ```
            因此, 列表生成式也可以使用两个变量来生成list:
            ```
            >>> d = ['x': 'A', 'y': 'B', 'z': 'C']
            >>> [k + '=' + v for k, v in d.items()]
            ['y=B', 'x=A', 'z=C']
            ```
            最后把一个list中所有的字符串变成小写:
            ```
            >>> L = ['Hello', 'World', 'IBM', 'Apple']
            >>> [slower() for s in L]
            ['hello', 'world', 'ibm', 'apple']
            - 练习
        - 问题: 如果list中既包含字符串，又包含整数，由于非字符串类型没有lower()方法，所以列表生成式会报错：
            ```
            >>> L = ['Hello', 'World', 18, 'Apple', None]
            >>> [s.lower() for s in L]
            Traceback (most recent call last):
            File "<stdin>", line 1, in <module>
            File "<stdin>", line 1, in <listcomp>
            AttributeError: 'int' object has no attribute 'lower'
            ```
            使用内建的`isinstance`函数可以判断一个变量是不是字符串:
            ```
            >>> x = 'abc'
            >>> y = 123
            >>> isinstance(x, str)
            True
            >>> isinstance(y, str)
            False
            ```
            请修改列表生成式, 通过添加`if`语句保证列表生成式能正确地执行
        - 答案: 
            ```
            [x.lower() for x in L if isinstance(x, str) == True]
            ```
    - 生成器
        - 产生背景: 通过列表生成式, 我们可以直接创造一个列表. 但是, 受到内存限制, 列表容量肯定是有限的. 而且, 创建一个包含100万个元素的列表, 不仅占用很大得到存储空间, 而且如果我们仅仅需要访问前面几个元素, 那后面绝大多数元素占用的空间都白白浪费了.      
        所以, 如果列表元素可以按照某种算法推算出来, 那我们是否可以在循环的过程中不断推算出后续元素呢? 这样就不必创建完整的list, 从而节省大量的空间. 在Python中, 这种一边循环一边计算的机制, 称为生成器(generator)
        - 创建方法
            1. 把列表生成式的`[]`换成`()`
                ```
                >>> L = [x*x for x in range(10)]
                >>> L 
                [0,1,4,9,16,25,36,49,64,81]
                >>> g = (x*x for x in range(10))
                >>> g
                <generator object <genexpr> at 0x1022ef630>
                ```
                由于generator保存的是算法, 所以可以通过`next()`来获得generator的下一个返回值,但这样太麻烦了, 所以我们创建一个generator后, 基本永远不会调用`next()`, 而是通过`for`循环来迭代它.
            2. 如果一个函数定义中包括`yield`关键字, 那么这个函数就不再是一个普通函数, 而是一个generator:
                ```
                >>> f = fib(6)
                >>> f
                <generator object fib at 0x104feaaa0>
                ```
                此方法不像方法1那样自动中断, 而是`next`到`yield`关键字才中断, 再次执行`next`则从上次返回时的`yield`语句处继续执行.
        - 用处: generator非常强大, 如果推算的算法比较复杂, 用类似列表生成式的`for`循环无法实现的时候, 还可以用函数来实现.
        - 练习
            - 题目: 杨辉三角定义如下:
                ```
                          1
                        1   1
                    1   2   1
                    1   3   3   1
                1   4   6   4   1
                1   5   10  10  5   1
                ```
                把每一行看做一个list, 试写一个generator, 不断输出下一行的list.
            - 答案:
                ```
                def triangles(max):
                ary_1 = [1,1]
                print([1])
                print([1, 1])
                n = 3
                while n <= max:
                    i = 0
                    a = 0
                    ary_2 = [1,1]
                    while i < len(ary_1)-1:
                        num = ary_1[i] + ary_1[i+1]
                        ary_2.insert(i+1, num)
                        i += 1
                    ary_1 = ary_2
                    yield ary_1
                    n += 1
                ```
    - 迭代器
        - 背景
            - 可以直接作用于`for`循环的数据类型有以下几种:
                1. 集合数据类型, 如`list`, `tuple`, `dict`, `set`, `str`等;
                2. `generator`, 包括生成器和带`yield`的generator function.
            - 这些可以直接作用于`for`循环的对象统称为可迭代对象: `Iterable`.
            - 可以使用`isinstance()`判断一个对象是否是`Iterable`对象:
                ```
                >>> from collections import Iterable
                >>> isinstance([], Iterable)
                True
                >>> isinstance({}, Iterable)
                True
                >>> isinstance('abc', Iterable)
                True
                >>> isinstance((x for x in range(10)), Iterable)
                True
                >>> isinstance(100, Iterable)
                False
                ```
            - 而生成器不但可以作用于`for`循环, 还可以被`next()`函数不断调用并返回下一个值, 直到最后抛出`StopIterable`错误表示无法继续返回下一个值了.
        - 定义: 可以被`next()`函数调用并不断返回下一个值的对象称为迭代器: `Iterable`.
            - 可以使用`isinstance()`判断一个对象是否是`Iterable`对象:
                ```
                >>> from cokkections import Iterable
                >>> isinstance((x for x in range(10)), Iterable)
                True
                >>> isinstance([], Iterator)
                False
                >>> isinstance({}, Iterator)
                False                
                >>> isinstance('abc', Iterator)
                False
                ```
        - 范围: 生成器都是`Iterable`对象, 但`list`, `dict`, `str`虽然是`Iterable`, 却不是`Iterator`.
        - 转换: 把`list`, `dict`, `str`等`Iterable`变成`Iterator`可以使用`iter()`函数:
            ```
            >>> isinstance(iter([]), Iterator)
            True
            >>> isinstance(iter('abc'), Iterator)
            True
            ```
        - 问题: 为什么`list`, `dict`, `str`等数据类型不是`Iterator`?
            - 回答: 这是因为Python的`Iterator`对象表示的是一个数据流, Iterator对象可以被`next()`函数调用并不断返回下一个数据, 直到没有数据时抛出`StopIteration`错误. 可以把这个数据流看做是一个有序系列, 但我们不能提前知道序列的长度, 只能不断通过`next()`函数实现按需计算下一个数据, 索引`Iterator`的计算是惰性的, 只有在需要返回下一个数据时它才会计算.
                - `Iterator`甚至可以表示一个无限大的数据流, 例如全体自然数. 而使用list是永远不可能存储全体自然数的.
        - 小结
            - 凡是可作用于`for`循环的对象都是`Iterator`类型
            - 凡是可作用于`next()`函数的对象都是`Iterator`类型, 它们表示一个惰性计算的序列
            - <span style='background-color: blue'>Python的`for`循环本质上就是通过不断调用`next()`函数来实现的</span>
- 函数式编程
    - 高阶函数(Higher-order function)
        - 定义: 能接收别的的函数作为参数的函数
            - 例
                ```
                def add(x, y, add):
                    return add(x) + add(y)
                ```
        - map/reduce
            - map
                - 原理: `map()`接收两个参数, 一个是函数, 一个是`Iterable`, `map`将传入的函数依次作用到序列的每一个元素, 并把结果作为新的`Iterator`返回. 例如:
                    ```
                    >>> def f(x):
                    ...     return x * x
                    ...
                    >>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
                    >>> list(r)
                    [1, 4, 9, 16, 25, 36, 49, 64, 81]
                    ```
            - reduace
                - 原理: `reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上, 这个函数必须接收两个参数, `reduce`把结果继续和序列的下一个元素做累积计算, 其效果是:
                    ```
                    reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
                    ```
                如果还是不明白, 就举个例子:
                    ```
                    >>> from functools import reduce
                    >>> def add(x, y): 
                    ...     return x + y
                    ...     
                    >>> reduce(add, [1, 3, 5, 7, 9])
                    25
                    ```
                - 优秀样例
                    ```
                    >>> from functools import reduce
                    >>> def fn(x, y):
                    ...     return x * 10 + y
                    ...
                    >>> def char2num(s):
                    ...     return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9', 9}[s]
                    ...
                    >>> reduce(fn, map(char2num, '13579'))
                    13579
                    ```
                - 练习
                    - 题目: 利用map()函数，把用户输入的不规范的英文名字，变为首字母大写，其他小写的规范名字。输入：['adam', 'LISA', 'barT']，输出：['Adam', 'Lisa', 'Bart']
                    - 答案:
                        ```
                        from functools import reduce

                        def normalize(name):
                            def lower2capital(s):
                                dict = {'a': 'A', 'b': 'B', 'c': 'C', 'd': 'D', 'e': 'E', 
                                        'f': 'F', 'g': 'G', 'h': 'H', 'i': 'I', 'j': 'J',
                                        'k': 'K', 'l': 'L', 'm': 'M', 'n': 'N', 'o': 'O',
                                        'p': 'P', 'q': 'Q', 'r': 'R', 's': 'S', 't': 'T', 
                                        'u': 'U', 'v': 'V', 'w': 'W', 'x': 'X', 'y': 'Y', 
                                        'z': 'Z'}
                                if s in dict:
                                    return dict[s]
                                else:
                                    return s
                            def capital2lower(s):
                                dict = {'A': 'a', 'B': 'b', 'C': 'c', 'D': 'd', 'E': 'e',
                                        'F': 'f', 'G': 'g', 'H': 'h', 'I': 'i', 'J': 'j',
                                        'K': 'k', 'L': 'l', 'M': 'm', 'N': 'n', 'O': 'o',
                                        'P': 'p', 'Q': 'q', 'R': 'r', 'S': 's', 'T': 't',
                                        'U': 'u', 'V': 'v', 'W': 'w', 'X': 'x', 'Y': 'y',
                                        'Z': 'z'}
                                if s in dict:
                                    return dict[s]
                                else:
                                    return s
                            def str_add(a, b):
                                return a + b
                            def main_fun(str_test):
                                return lower2capital(str_test[0]) + reduce(str_add, map(capital2lower, str_test[1:]))
                            return map(main_fun, name)
                        
                        L = ['adam', 'LIdA', 'barT']
                        print(list(normalize(L)))
                        ```
        - filter
            - 定义: `filter()`函数用于过滤序列. 和`map()`类似, `filter()`也接收一个函数和一个序列. 和`map()`不同的是, `filter()`把传入的函数依次作用于每个元素, 然后根据返回值是`True`还是`False`决定保留还是丢弃该元素.         
            例如, 在一个list中, 删掉偶数, 只保留奇数, 可以这么写:
            ```
            def is_odd(n):
                return n % 2 == 1
                
            list(filter(is_odd, [1 ,2, 4, 5, 6, 9, 10, 15]))
            ```
            使用`filter()`这个函数, 关键在于正确实现一个"筛选"函数.
            - 注意: `filter()`返回的是一个`Iterator`, 即一个惰性序列, 所以要使用`list()`函数来强迫`filter()`完成计算结果
        - 切片补充
            - L[10:20:2]: 前10-20个数中, 每两个娶一个
            - L[:50:5]: 前50的数中, 每五个取yige
            - L[::-1]: 反转列表
                - 小结: 从L[::-1]可以看出, 每n个数取一个的本质是从给定下标开始每次前进n个下标,取一个数. 而L[::-1]则每次需要前进-1个下标, 即从起点下标0开始, 第一次前进-1个下标, 到下标-1, 而python中规定, 下标-1表示为倒数第一个数, 以此类推, 则L[::-1]可取到反转的列表
        - sorted
                - `sorted()`函数可以接收一个`key`来实现自定义的排序
                使用`sorted()`函数排序的关键在于实现一个映射函数.
                - 练习
                    - 问题: 假设我们用一组tuple表示学生名字和成绩：
                    L = [('Bob', 75), ('Adam', 92), ('Bart', 66), ('Lisa', 88)]
                    请用sorted()对上述列表分别按名字和成绩高低排序
                    - 答案:
                        ```
                        # 名字排序
                        L = [('Bob', 75), ('Adam', 92), ('Bart', 66), ('Lisa', 88)]
                        def by_name(t):
                            return t[0]
                        L2 =sorted(L, key=by_name, reverse=True)
                        print(L2)
                        
                        #成绩高低排序
                        L = [('Bob', 75), ('Adam', 92), ('Bart', 66), ('Lisa', 88)]
                        def by_name(t):
                            return t[1]
                        L2 =sorted(L, key=by_name, reverse=True)
                        print(L2)
                        ```
    - 返回函数
        - 解释: 高阶函数除了可以接受函数作为参数外, 还可以把函数作为结果值返回.
        - 例子
            ```
            def lazy_sum(*args):
                def sum():
                    ax = 0
                    for n in args:
                        ax = ax + n
                    return ax
                return sum
            ```
            当我们调用`lazy_sum()`时, 返回的并不是求和结果, 而是求和函数:
            ```
            >>> f = lazy_sum(1,3,5,7,9)
            >>> f
            <function lazy_sum.<locals>.sum at 0x101c6ed90>
            ```
            调用函数`f`时, 才真正计算求和的结果:
            ```
            >>> f()
            25
            ```
            在这个例子中, 我们在函数`lazy_sum`中定义了函数`sum`, 并且, 内部函数`sum`可以引用外部函数`lazy_sum`的参数和局部变量, 当`lazy_sum`返回函数`sum`时, 相关参数和变量都保存在返回的函数中, 这种称为"闭包(Closure)"的程序结构具有极大的威力.
            - 注意, 当我们调用`lazy_sum()`时, 每次调用都会返回一个新的函数, 即使传入相同的参数
        - 注意: <span style="background-color:blue">返回的函数并没有立即执行, 而是直到调用了`f()`才执行. 例:</span>
            ```
            def count():
                fs = []
                for i in range(1, 4):
                    def f():
                        return i*i
                    fs.append(f)
                return fs
            f1, f2, f3 = count()
            ```
            在上面的例子中, 每次循环, 都创建了一个新的函数, 然后, 把创建的3个函数都返回了.
            你可能认为调用`f1()`, `f2()`, `f3()`结果应该是`1`, `4`, `9`, 但实际结果全部是`9`, 正好呼应了本段开头所说的
            因此牢记一点:
            - <span style="background-color:blue">返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。</span>
            - 如果一定要引用循环变量怎么办? 方法是再创建一个函数, 用该函数的参数绑定循环变量当前的值, 无论该循环变量如何更改, 已绑定到函数参数的值不变:
                ```
                def count():
                    def f(j):
                        def g():
                            return j*j
                        return g
                    fs = []
                    for i in range(1, 4):
                        fs.append(f(i)) #
                    return fs
                ```
                缺点是代码较长, 可利用lambda函数缩短代码.
        - 练习: 利用闭包返回一个计数器函数, 每次调用它返回递增整数
            - 答案: 
                ```
                def createCounter():
                    n = 0
                    def counter():
                        nonlocal n
                        return n += 1
                    return counter
                ```
            - 小结: 由于闭包的作用, 在createCounter执行完成后, 闭包使得Python的垃圾回收机制不会收回createCounter所占createCounter的内部函数counter的执行需要依赖createCounter中的变量, 因此在函数createCounter返回后, n的值始终存在, 这样每次执行counterA=createCounter(), n都会+1
    - 匿名函数
        - 表示形式: 关键词`lambda`表示匿名函数, 冒号前的`x`表示函数参数, 例如:
            ```
            lambda x: x * x
            ```
            相当于
            ```
            def f(x)
                return x * x
            ```
        - 限制: 只能有一个表达式, 不用写`return`, 返回值就是该表达式的结果.
    - 装饰器
        - 解释
            - 设置一个函数
                ```
                >>> def now():
                ...     print('2015-3-25')
                ...
                >>> f = now
                >>> f()
                2015-3-25
                ```
                现在, 假设我们要增强`now()`函数的功能, 比如, 在函数调用前后自动打印日志, 但又不希望修改函数的定义, 这种在代码运行期间动态增加功能的方式, 称之为"装饰器"(Decorator).      
                本质上, decorator就是一个返回函数的高阶函数. 所以我们可以这样定义能打印日志的decorator:
                ```
                def log(func):
                    def wrapper(*args, **kw):
                        print('call %s():' % func.__name__)
                        return func(*args, **kw)
                    return wrapper
                ```
                `log`是一个decorator, 可以接受一个函数作为参数, 并返回一个函数        
                利用Python的@语法, 把decorator置于函数的定义处:
                ```
                @log
                def now():
                    print('2015-3-25')
                ```
                调用now()的结果是:
                ```
                >>> now()
                call now():
                2015-3-25
                ```
                把`@long`放到`now()`函数的定义处, 相当于执行了语句:
                ```
                now = log(now)
                ```
                从结果可以看出, func在`wrapper`函数中的__name__属性还是now, 但在`log`这个decorator执行完之后, now的__name__属性已经变成了wrapper.
        - 进阶
            - decorator本身传入参数
                - 需要编写一个返回decorator的高阶函数, 写出来会更复杂. 比如, 要自定义log的文本:
                    ```
                    def log(text):
                        def decorator(func):
                            def wrapper(*args, **kw):
                                print('%s %s():' % (text, func.__name__))
                                return func(*args, **kw)
                            return wrapper
                        return decorator
                    ```
                    用法如下:
                    ```
                    @log('execute')
                    def now():
                        print('2015-3-25')
                    ```
                    执行结果如下:
                    ```
                    >>> now()
                    execute now():
                    2015-3-25
                    ```
                    效果是:
                    ```
                    >>> now = log('execute')(now)
                    ```
                    上面的语句首先执行log('execute')，返回的是decorator函数，再调用返回的函数，参数是now函数，返回值最终是wrapper函数。
        - 原始函数的`__name__`等属性
            - 由于`__name__`属性发生变化, 所以需要把原始函数的`__name__`等属性复制到`wrapper()`函数中, 否则, 有些依赖函数签名的代码执行就会出错.
                - `functools.wraps`: 用来解决上述问题
                - 完整的decorator的写法:
                    ```
                    import functools

                    def log(func):
                        @functools.wraps(func)
                        def wrapper(*args, **kw):
                            print('call %s():' % func.__name__)
                            return func(*args, **kw)
                        return wrapper
                    ```
                    或者针对带参数的decorator:
                    ```
                    import functools

                    def log(text):
                        def decorator(func):
                            @functools.wraps(func)
                            def wrapper(*args, **kw):
                                print('%s %s():' % (text, func.__name__))
                                return func(*args, **kw)
                            return wrapper
                        return decorator
                    ```
        - 练习
            - 题目一: 设计一个decorator, 它可作用于任何函数, 并打印该函数的执行时间:
                - 答案:
                    ```
                    import functools
                    import time
                    import datetime

                    def log(text):
                        def decorator(func):
                            @functools.wraps(func)
                            def wrapper(*args, **kw):
                                begin = datetime.datetime.now()
                                result = func(*args, **kw)
                                end = datetime.datetime.now()
                                print('%s %s %s' % (func.__name__, text, end - begin))
                                return result
                            return wrapper
                        return decorator

                    @log('的运行时间为:')
                    def fast(x, y):
                        time.sleep(0.012)
                        print('fast() run')
                        return x + y

                    print(fast(1, 2))
                    ```
                - 题目一小结: `fast`的实际运行情况是
                    ```
                    fast = log('的运行时间为:')(fast)
                    ```
                    即把`fast`作为参数带入`log`中运行
            - 题目二: 写出一个`@log`的decorator, 是它既支持`@log`又支持`@log('text')`
                - 答案: 将`def log(text)`变为`def log(*text)`即可
    - 偏函数
        - 作用: `functools.partial`就是帮助我们创建一个偏函数的, 不需要我们自己定义`int2()`, 可以直接使用下面的代码创建一个新的函数`int2`:
            ```
            >>> import functools
            >>> int2 = functools.partial(int, base=2)
            >>> int2 = ('100000')
            64
            >>> int2 = ('101010')
            85
            ```
            所以, 简单总结`functools.partial`的作用就是, 把一个函数的某些参数给固定住, 返回新函数, 调用这个新函数会更简单.
        - 小结: 当函数的参数个数太多, 需要简化时, 使用`functools.partial`可以创建一个新的函数, 这个新函数可以固定住原函数的部分参数, 从而在调用时更简单.
- 模块
    - 产生原因: 随着程序代码越写越多, 在一个文件里代码就会越来越长, 越来越不容易维护. 为了编写可维护的代码, 我们把很多函数分组, 分别放到不同的文件里, 这样, 每个文件包含的代码就相对较少, 很多编程语言都采用这种组织代码的方式. 在Python中, 一个py文件就称之为一个模块(Module).
    - 每个包`package`下面都会有一个`__init__.py`的文件, 这个文件必须存在, 否则Python就把这个目录当成普通目录, 而不是一个包.
    - 总结: 
        - 创建自己的模块时, 要注意:
            - 模块名要遵循Python变量命名规范, 不要使用中文/特殊字符;
            - 模块名不要和系统模块名冲突, 最好先查看系统是否已存在该模块, 检查方法是在Python交互环境执行`import abc`, 若成功则说明系统存在此模块.
    - 使用模块
        - 一个`hello`模块, 调用`sys`模块
            ```
           #!/usr/bin/env python3
            # -*- coding: utf-8 -*-

            ' a test module '

            __author__ = 'Michael Liao'

            import sys

            def test():
                args = sys.argv
                if len(args)==1:
                    print('Hello, world!')
                elif len(args)==2:
                    print('Hello, %s!' % args[1])
                else:
                    print('Too many arguments!')

            if __name__=='__main__':
                test() 
            ```
            `sys`模块有一个`argv`变量, 用list存储了命令行的所有参数.       
            注意最后两行代码:
            ```
            if __name__=='__main__':
                test()
            ```
            当且仅当我们在命令行`hello`模块文件时, Python解释器把特殊变量`__name__`置为`__main__`, 而如果在其它地方导入该`hello`模块时, `if`判断将失效, 因此, 这种`if``测试可以让一个模块通过命令行运行时执行一些额外的代码, 最常见的
    - 作用域
        - 公开变量
            - 正常函数和变量名
            - 类似`__xxx__`的特殊变量
        - 非公开变量
            - 类似`_xxx`和`__xxx`的函数或变量: 不应该被直接引用, 而不是不能被直接引用. 因为Python并没有一种方法可以完全限制访问private函数或变量
        - 编程规范: 外部不需要引用的函数全部定义成private, 只有外部需要引用的函数才定义为public.
- 面对对象编程
    - 类和实例
        - 可以自由地给一个实例变量绑定属性, 比如, 给实例`bart`绑定一个`name`属性:
            ```
            >>> class Student(object):
            >>>    pass  
            >>> bart = Student()
            >>> bart
            <__main__.Student object at 0x10a67a590>
            >>> Student
            <class '__main__.Student'>
            >>> bart.name = 'Bart Simpson'
            >>> bart.name
            'Bart Simpson'
            ```
        - 绑定属性
            - 由于类可以起到模板的作用, 因此, 可以在创建实例的时候, 把一些我们认为必须绑定的属性强制填写进去, 通过定义一个特殊的`__init__`方法, 在创建实例的时候, 就把`name`, `score`等属性绑上去:
                ```
                class Student(object):
                    def __init__(self, name, score):
                        self.name = name
                        self.score = score
                ```
                注意, `__init__`方法的第一个参数永远是`self`, 表示创建的实例本身, 因此, 在`__init__`方法内部, 就可以把各种属性绑定到`self`, 因为`self`就指向创建的实例本身.
        - 传送参数
            - 有了__init__方法，在创建实例的时候，就不能传入空的参数了，必须传入与__init__方法匹配的参数，但self不需要传，Python解释器自己会把实例变量传进去：
                ```
                >>> bart = Student('Bart Simpson', 59)
                >>> bart.name
                'Bart Simpson'
                >>> bart.score
                59
                ```
                和普通的函数相比, 在类中定义的函数只有一点不同, 就是第一个参数永远是实例变量`self`, 而且调用时不用传递该参数.
        - 数据封装
            - 封装数据的函数被称为类的方法
            - 我们从外部看`Student`类, 只需要知道, 创建实例需要给出`name`和`score`, 调用很容易, 但却不用知道内部实现的细节.
        - 小结
            - 类是创建实例的模板，而实例则是一个一个具体的对象，各个实例拥有的数据都互相独立，互不影响
            - 方法就是与实例绑定的函数, 和普通函数不同, 方法可以直接访问实例的数据
            - <span style="background-color:yellow">和静态语言不同，Python允许对实例变量绑定任何数据，也就是说，对于两个实例变量，虽然它们都是同一个类的不同实例，但拥有的变量名称都可能不同：</span>
    - 访问限制
        - 限制修改属性
            - 例: 从前面Student类的定义来看, 外部代码可以自由修改一个实例的`name`/`score`属性:
                ```
                >>> bark = Student('Bart Simpson', 59)
                >>> bart.score
                59
                >>> bart.score = 99
                >>> bart.score
                99
                ```
                如果要让内部属性不被外部访问, 可以把属性的名称前加两个下划线`__`, 在Python中, 实例的变量名如果以`__`开头, 就变成了一个私有变量(private), 只有内部可以访问.
        - 允许获取属性
            - 如果外部代码要获取name和score怎么办? 可以给Student类增加`get_name`和`get_score`的方法
        - 允许修改属性
            - 可以再给Student类增加`set_score`方法
            改后的Student类如下:
            ```
            class Student(object):

                def __init__(self, name, score):
                    self.__name = name
                    self.__score = score

                def print_score(self):
                    print('%s: %s' % (self._name, self._score))

                def get_name(self):
                    return self.__name

                def get_score(self):
                    return self.__score
                
                def set_score(self):
                    self._score = score
            ```
    - 继承和多态
        - 继承
            - 解释: 在OOP程序设计中, 当我们定义一个class的时候, 可以从某个现有的class继承, 新的class称为子类(Subclass), 而被继承的class称为基类/父类或超类(Base class/Super class).
        - 多态
            - 解释: 当子类和父类都存在相同的`method()`方法时, 我们说, 子类的`method()`覆盖了父类的`method()`, 在代码运行的时候, 总是会调用子类的`method()`. 这样, 我们就获得了继承的另一个好处: 多态.
            - 作用: 调用方只管调用, 不管细节, 而当我们新增一种`Animal`的子类时, 只要确保`run()`方法编写正确, 不用管原来的代码是如何调用的. 这就是著名的"开闭"原则: 
                - 对扩展开放: 允许新的`Animal`子类
                - 对修改封闭: 不需要修改依赖`Animal`类型的`run_twice()`等函数
            - 示例代码:
                ```
                class Animal(object):
                    def run(self):
                        print('Animal is running...')

                class Dog(Animal):
                    def run(self):
                        print('Dog is running...')
                    def eat(self):
                        print('Eating meat...')
                class Cat(Animal):
                    def run(self):
                        print('Cat is running...')
                class Fish(Animal):
                    pass
                    
                def run_twice(animal):
                    animal.run()
                    animal.run()
                run_twice(Fish())
                ```
        - 静态语言 vs 动态语言
            - <span style='background-color: yellow'>动态语言的鸭子类型特点决定了继承不像静态语言那样是必须的。</span>
    - 获取对象信息
        - 使用type(): 判断基本类型
            - 如果一个变量指向函数或者类, 也可以用`type()`判断
            - 判断对象是否是函数: 可以使用`types`模块中定义的常量:
                ```
                >>> import types
                >>> def fn():
                ...     pass
                ...
                >>> type(fn)==types.FunctionType
                True
                >>> type(abs)==types.BuiltinFunctionType
                True
                >>> type(lambda x: x)==types.LambdaType
                True
                >>> type((x for x in range(10)))==types.GeneratorType
                True
                ```
        - getattr()/setattr()/hasattr(): 通过上述方法, 我们可以直接操作一个对象的状态:
            ```
            >>> class MyObject(object):
            ...     def __init__(self):
            ...         self.x = 9
            ...     def power(self):
            ...         return self.x * self.x
            ...
            >>> obj = MyObject()
            >>> hasattr(obj, 'x') # 有属性'x'吗？
            True
            >>> obj.x
            9
            >>> hasattr(obj, 'y') # 有属性'y'吗？
            False
            >>> setattr(obj, 'y', 19) # 设置一个属性'y'
            >>> hasattr(obj, 'y') # 有属性'y'吗？
            True
            >>> getattr(obj, 'y') # 获取属性'y'
            19
            >>> obj.y # 获取属性'y'
            19
            ```
    - 实例属性和类属性
        - 类属性: 类本身的属性, 可以直接在class中定义,
            - 当我们定义了一个类属性后, 这个属性虽然归类所有, 但类的所有实例都可以访问到.
        - 注意: 千万不要对实例属性和类属性使用相同的名字, 因为相同名字的实例属性将屏蔽掉类属性
        - 练习
            - 题目: 为了统计学生人数，可以给Student类增加一个类属性，每创建一个实例，该属性自动增加
            - 答案: 
                ```
                class Student(object):
                count = 0
                def __init__(self, name):
                    self.name = name
                    Student.count += 1
                ```
            - 关于`Class.attr`和`self.attr`的一点思考:      
                在上述类中, 如果将第五行`Student.count`改为`self.count`, 则结果为
                ```
                bart = Student('Bart')
                print(Student.count)
                alex = Student('Alex')
                print(Student.count)
                grey = Student('Grey')
                print(Student.count)
                0
                0
                0
                ```
                原因: `self`指向的是调用类的实例(如:`bart`/`alex`/`grey`)而不是`Student`类, 实例的count属性继承至类的count属性, 而实例的count属性改变不会引起类的count属性的改变, 但类的count属性的改变会引起实例的count属性的改变, 看下面的例子:
                ```
                class Student(object):
                    count = 0
                    def __init__(self, name):
                        self.name = name
                        Student.count += 1
                        self.count += 1
                bart = Student('Bart')
                print(Student.count)
                print(bart.count)
                alex = Student('Alex')
                print(Student.count)
                print(alex.count)
                grey = Student('Grey')
                print(Student.count)
                print(grey.count)
                1
                2
                2
                3
                3
                4
                ```
                为何会有这样的结果呢? 因为每新建一个实例, self的count属性都会复制类的count属性, 并且类的count属性和self的count属性都+1, 所以当调用`self.count`在`Class.count`之后是, 每次打印会比`Class.count`多1
                - 小结: 
                    1. `self`对象指向的是调用它的实例
                    2. 实例的属性继承自类的属性, 并且实例的属性是继承自定义实例属性的那一刻的类的属性
- 面向对象高级编程
    - 解释: 数据封装/继承/多态只是OOP设计中最基本的3个概念. 在Python中, OOP还有很多高级特性, 允许我们写出非常强大的功能, 如: 多重继承/定制类/元类等概念
    - 动态语言 vs 静态语言
        - 动态语言(Dynamically Typed Language)
            - 特性: 程序在运行时可以改变其结构：新的函数可以被引进，已有的函数可以被删除等在结构上的变化，类型的检查是在运行时做的
        - 静态语言(Statically Typed Language)
            - 特性: 在运行前编译时检查类型。在写代码时，没声明一个变量必须指定类型. 无法在运行时改变其结构
        - 优缺点: 静态类型语言的主要优点在于其结构非常规范，便于调试，方便类型安全；缺点是为此需要写更多的类型相关代码，导致不便于阅读、不清晰明了。动态类型语言的优点在于方便阅读，不需要写非常多的类型相关的代码；缺点自然就是不方便调试，命名不规范时会造成读不懂，不利于理解等。顺便说一下，现在有这样一种趋势，那就是合并动态类型与静态类型在一种语言中，这样可以在必要的时候取长补短，Boo就是一个很好的试验性例子.
    - 使用`__slots__`       
        - 为类动态绑定方法: 例:
            ```
            >>> def set_score(self, score):
            ...     self.score = score
            ...
            ```
            给class绑定方法后, 所有实例都可调用
            >>> Student.set_score = set_score  
        为了限制实例的属性, Python允许在定义class的时候, 定义一个特殊的`__slots__`变量, 来限制该class实例能添加的属性. 例:
        ```
        class Student(object):
            __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
        ```
        使用`__slots__`要注意, 其定义的属性仅对当前类的实例起作用, 对继承的子类不起作用
    - 使用@property
        - 解释: 通过`set_score()`和`get_score()`方式的调用方法略显复杂, 没有直接用属性这么直接简单.
        `@property`可以实现既能检查参数, 又可以用类似属性这么简单的方式来访问类的变量.
        只需要加上`@property`就可以了, 此时, `@property`本身又创建了另一个装饰器`@score.setter`, 负责把一个setter方法编程属性赋值, 于是, 我们就拥有一个可控的属性操作:
        ```
        class Student(object):

            @property
            def score(self):
                return self._score

            @score.setter
            def score(self, value):
                if not isinstance(value, int):
                    raise ValueError('score must be an integer!')
                if value < 0 or value > 100:
                    raise ValueError('score must between 0 ~ 100!')
                self._score = value
        ```
        ```
        >>> s = Student()
        >>> s.score = 60 # OK，实际转化为s.set_score(60)
        >>> s.score # OK，实际转化为s.get_score()
        60
        >>> s.score = 9999
        Traceback (most recent call last):
        ...
        ValueError: score must between 0 ~ 100!
        ```
        还可以定义只读属性, 即: 只定义getter()方法, 不定义setter方法就是一个只读属性:
        ```
        class Student(object):

            @property
            def birth(self):
                return self._birth

            @birth.setter
            def birth(self, value):
                self._birth = value

            @property
            def age(self):
                return 2015 - self._birth
        ```
        上面的`birth`是可读写属性, 而`age`就是`只读`属性, 因为`age`可以根据`birth`和当前时间计算出来
        - 练习
            - 题目: 请利用@property给一个Screen对象加上width和height属性，以及一个只读属性resolution
            - 答案: 
                ```
                class Screen(object):
                    @property
                    def width(self):
                        return self._width

                    @width.setter
                    def width(self, value):
                        self._width = value

                    @property
                    def height(self):
                        return self._height

                    @height.setter
                    def height(self, value):
                        self._height = value

                    @property
                    def resolution(self):
                        return self._width * self._height

                s = Screen()
                s.width = 1024
                s.height = 768
                print(s.resolution)
                ```
    - 多重继承
        - 作用: 通过多重继承, 一个子类可以同时获得多个父类的所有功能           
        - MixIn: 在设计类的继承关系时, 通常, 主线都是单一继承下来的, 例如, `Ostrich`继承自`Bird`. 但是, 如果需要"混入"额外的功能, 通过多重继承就可以实现. 这种设计通常称之为MixIn.
            - 一个子类可以拥有多个MixIn
            - 只允许单一继承的语言(如Java)不能使用MixIn的设计
    - 定制类
        - 注意: 看到类似`__slots__`这种变量或者函数名就要注意, 这些在Python中都有特殊用途.
        - __str__()
            - 作用: 自定义返回实例时的打印信息
                ```
                >>> class Student(object):
                ...     def __init__(self, name):
                ...         self.name = name
                ...     def __str__(self):
                ...         return 'Student object (name: %s)' % self.name
                ...
                >>> print(Student('Michael'))
                Student object (name: Michael)

                ```
                如果不是使用`print`, 而是在命令行中直接敲变量, 打印出来的实例还是不好看:
                ```
                >>> s = Student('Michael')
                >>> s
                <__main__.Student object at 0x00000213EE9C27B8>
                ```
                原因: 直接显示变量调用的不是`__str__()`而是`__repr__()`, 两者的区别是前者返回用户看到的字符串, 后者返回程序开发者看到的字符串.
                - 解决方法: 添加一行:
                    ```
                    __repr__ = __str__
                    ```
        - __iter__(): 如果一个类想被用于for ... in循环，类似list或tuple那样，就必须实现一个__iter__()方法，该方法返回一个迭代对象，然后，Python的for循环就会不断调用该迭代对象的__next__()方法拿到循环的下一个值，直到遇到StopIteration错误时退出循环。     
        以斐波那契(Fibonacci)为例, 写一个Fib类, 可以作用于for循环:
            ```
            class Fib(object):
                def __init__(self):
                    self.a, self.b = 0, 1
                def __iter__(self):
                    return self
                def __next__(self):
                    self.a, self.b = self.b, self.a + self.b
                    if self.a > 100000:
                        raise StopIteration()
                    return self.a
            for n in Fib():
                print(n)
            ```
        - __getitem__(): Fib实例虽然可以作用于for循环, 看起来和list有点像, 但是, 不行, 比如, 取第5个元素:
            ```
            >>> Fib()[5]
            Trackback (most recent call last):
                File "<stdin>", line 1, in <module>
            TypeError: 'Fib' object does not support indexing
            ```
            要想按下标取元素, 需要实现`__getitem()__`方法:
            ```
            class Fib(object):
                def __getitem__(self, n):
                    a, b = 1, 1
                    for x in range(n):
                        a, b = b, a + b
                    return a
            f = Fib()
            print(f[0])
            print(f[1])
            print(f[2])
            print(f[3])
            ```
            结果为:
            ```
            1
            1
            2
            3
            ```
            但list有一个神奇的切片方法
            ```
            >>> list(range(100))[5:10]
            [5,6,7,8,9]
            ```
            对于Fib却报错. 原因是`__getitem__()`传入的参数需要我们做判断并且写出判断后的处理方法
            ```
            class Fib(object):
                def __getitem__(self, n):
                    if isinstance(n, int):
                        a, b = 1, 1
                        for x in range(n):
                            a, b = b, a + b
                        return a
                    if isinstance(n, slice):
                        start = n.start
                        stop = n.stop
                        if start is None:
                            start = 0
                        a, b = 1, 1
                        L = []
                        for x in range(stop):
                            if x >= start:
                                L.append(a)
                            a, b = b, a + b
                        return L
            f = Fib()
            print(f[0:5])
            ```
            如果需要其它功能, 可以继续自定义. 如: 对step参数进行处理/对负数进行处理
            与之对应的还有`__setitem__()`方法和`__delitem__()`方法
        - __getattr__(): 可以动态返回一个属性
            - 注意: 
                - 只有找不到属性才会调用`__getattr__()`
                - 如果调用其它没有的函数, 不会报错, 而会返回None, 因为`__getattr__()`默认返回None. 为了让class只响应特定的几个属性, 对其它属性报错, 我们要按照正常属性缺失错误一样, 抛出`AttributeError`:
                    ```
                    class Student(object):

                        def __getattr__(self, attr):
                            if attr=='age':
                                return lambda: 25
                            raise AttributeError('\'Student\' object has no attribute \'%s\'' % attr)
                    ```
                    这实际上可以把一个类的所有属性和方法调用全部动态化处理了.
            - <span style="background-color: red>关于`__getattr__()`的链式调用的应用的一点解析</span>
                - 实例: 现在很多网站都搞REST API，比如新浪微博、豆瓣啥的，调用API的URL类似：

                    http://api.server/user/friends
                    http://api.server/user/timeline/list
                    如果要写SDK，给每个URL对应的API都写一个方法，那得累死，而且，API一旦改动，SDK也要改。

                    利用完全动态的__getattr__，我们可以写出一个链式调用：
                    ```
                    class Chain(object):

                        def __init__(self, path=''):
                            self._path = path

                        def __getattr__(self, path):
                            return Chain('%s/%s' % (self._path, path))

                        def __str__(self):
                            return self._path

                        __repr__ = __str__
                    ```
                    尝试:
                    ```
                    >>> Chain().status.user.timeline.list
                    '/status/user/timeline/list'    
                    ```
                - 疑问: Chain().status.user.timeline.list在类Chain()内部是如何工作的才导致输出为: 
                    '/status/user/timeline/list'?
                - 解析: 
                    1. 只分析一层属性和两层属性, 即: `Chain().status`与`Chain().status.user`. 在类Chain()中添加print, 新代码如下:
                        ```
                        class Chain(object):
                            
                            def __init__(self, path=''):
                                self._path = path
                                print("init path is:" + self._path)
                            
                            def __getattr__(self, path):
                                print("get path is:" + path)
                                return Chain('%s/%s' % (self._path, path))

                            def __str__(self):
                                return self._path

                            __repr__ = __str__
                        # print(c)
                        print('---------------------------')
                        print(Chain().status)
                        print('---------------------------')
                        print(Chain().status.user)
                        print('---------------------------')
                        ```
                    2. 结果是:
                        ```
                        ---------------------------
                        init path is:
                        get path is:status
                        init path is:/status
                        /status
                        ---------------------------
                        init path is:
                        get path is:status
                        init path is:/status
                        get path is:user
                        init path is:/status/user
                        /status/user
                        ---------------------------
                        ```
                    3. 分析结果: 
                        1. `print(Chain().status)`经历了两次`__init__`, 再结合源代码中的`return Chain('%s/%s' % (self._path, path))`分析, 可知: `Chain`的新实例的`status`属性触发`__getattr__`方法, 而该方法对`Chain()`新实例进行了递归操作, 带入新实例的值为`/status`. 此时, 新实例二没有属性, 因此不会调用`__getattr__`方法. 而`print(Chain('/status'))`会调用`__str__`方法显示参数`path`即`/status`
                        2. `print(Chain().status.user)`: `1`中的新实例二会在`2`中拥有属性`user`, 再次调用`__getattr__`, `*.user`之后的属性会再再次调用`__getattr__`, 从而打印出相应路径