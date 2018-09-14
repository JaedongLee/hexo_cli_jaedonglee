---
title: Python_Note
date: 2017-11-09 11:13:04
tags:
category: Python
---
- Python基础
    - Python命令行: 可以直接在命令行中编写函数, 这样可以在cmd中调用函数
    - 使用list和tuple
        - list<!-- more -->
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
        - __call__
            - 用处: 不需要使用`instance.method()`来调用方法, 可以直接调用实例本身来调用默认方法. 例:
                ```
                class Student(object):
                    def __init__(self, name):
                        self.name = name

                    def __call__(self):
                        print('My name is %s.' % self.name)
                ```
                ```
                >>> s = Student('Michael')
                >>> s() # self参数不要传入
                My name is Michael.
                ```
                可以在`__call__()`中插入参数
    - 使用枚举类
        - 当我们需要定义常量时, 一个办法是用大写变量通过整数来定义, 例如月份:
            ```
            JAN = 1
            FEB = 2
            ...
            NOV = 11
            DEC = 12
            ```
            缺点是类型是`int`且仍是变量
        - 更好的方法是为这样的枚举类型定义一个class, 然后每个常量都是class的一个唯一实例. Python提供`Enum`类来实现这个功能:
            ```
            from enum import Enum
            Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
            ```
            这样我就获得了`Month`类型的枚举类        
            Month.value属性是自动赋给成员的`int`常量, 默认从`1`开始计数.
        - 自定义类
            - 为了更精确地控制枚举类型, 可以从`Enum`派生出自定义类:
                ```
                from enum import Enum, unique

                @unique
                class Weekday(Enum):
                    Sun = 0 # Sun的value被设定为0
                    Mon = 1
                    Tue = 2
                    Wed = 3
                    Thu = 4
                    Fri = 5
                    Sat = 6
                ```
                `@unique`装饰器可以帮助我们检查保证没有重复值.
            - 练习
                - 题目: 把`Student`的`gender`属性改造为枚举类型, 可以避免使用字符串
                - 答案: 
                    ```
                    from enum import Enum, unique

                    class Gender(Enum):
                        Male = 0
                        Female = 1

                    class Student(object):
                        def __init__(self, name, gender):
                            self.name = name
                            self.gender = gender

                    # 测试:
                    bart = Student('Bart', Gender.Male)
                    if bart.gender == Gender.Male:
                        print('测试通过!')
                    else:
                        print('测试失败!')
                    ```
    - 使用元类
        - type(): 使用`type()`可以动态创建class
            - 当我们用Python解释器载入一个模块时, 会依次执行该模块的所有语句, 执行结果就是动态创建一个class对象
            - 使用`type()`创建出新的类型:
                ```
                >>> def fn(self, name='world'): # 先定义函数
                ...     print('Hello, %s.' % name)
                ...
                >>> Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class
                >>> h = Hello()
                >>> h.hello()
                Hello, world.
                >>> print(type(Hello))
                <class 'type'>
                >>> print(type(h))
                <class '__main__.Hello'>
                ```
        - metaclass
            - 解释
                - 当我们定义了类以后, 就可以根据这个类创建出实例, 所以: 先定义类, 然后创建实例
                - 如果我们想创建类呢? 那就根据metaclass创建出类, 所以: 先定义metaclass, 然后创建类
                - 连接起来就是: 先定义metaclass, 再创建类, 最后创建实例
            - 动态修改的意义: 
                - 动态修改有什么意义? 直接在类的定义中添加方法不是更简单? 
                - 因为总会遇到需要通过metaclass修改类定义的. ORM就是一个典型的例子
            - ORM:
                - 解释: ORM全称为: "Object Relational Mapping`, 即对象-关系映射, 就是把关系数据库的一行映射为一个对象, 也就是一个类对应一个表, 这样, 写代码更简单, 不用直接操作SQL语句
                    - 要编写ORM框架, 所有类只能动态定义, 因为只有使用者才能根据表的结构定义处对应的类来
                - 编写ORM框架
                    1. 把调用接口写出来. 比如: 使用者如果使用这个ORM框架, 想定义一个`User`类来操作对应的数据库表`User`, 我们期待他写出这样的代码:
                        ```
                        class User(Model):
                            id = IntegerField('id')
                            name = StringField('name')
                            email = StringField('email')
                            password = StringField('password')

                        u = User(id=12345, name='Michael', email='test@test.com', password='12345')
                        u.save()
                        ```
                    2. `Field`及其子类`IntegerField`和`StringField`
                        ```
                        class Field(object):
                            def __init__(self, name, column_type):
                                self.name = name
                                self.column_type = column_type

                            def __str__(self):
                                return '<%s:%s>' % (self.__class__.__name__, self.name)
                        
                        class IntegerField(Field):
                            def __init__(self, name):
                                super(IntegerField, self).__init__(name, 'bigint')

                        class StringField(Field):
                            def __init__(self, name):
                                super(StringField, self).__init__(name, 'varchar(100)')
                        ```
                    
                    3. 编写最复杂的`ModelMetaclass`
                        ```
                        class ModelMetaclass(type)

                            def __new__(cls, name, bases, attrs)
                                if name == 'Model':
                                    return type.__new__(cls, name, bases, attrs)
                                print('Found model: %s' % name)
                                mappings = dict()
                                for k, v in mappings.keys():
                                    if isinstance(v, Field):
                                        print('Found mappings: %s' % (k, v))
                                        mappings[k] = v
                                for k in mappings.keys():
                                    attrs.pop(k)
                                attrs['__mappings__'] = mappings
                                attrs['__table__'] = name
                                return type.__new__(cls, name, bases, attrs)
                        ```
                    4. 编写基类`Model`
                        ```
                        class Model(dict, metaclass=ModelMetaclass):

                            def __init__(self, **kw):
                                super(Model, self).__init__(**kw)
                            
                            def __getattr__(self, key):
                                try:
                                    return self[key]
                                except KeyError:
                                    raise AttributeError(r"'Model' object has no attribute '%s'" $ key)
                            
                            def __setattr__(self, key, value):
                                self[key] = value

                            def save(self)
                                fields = []
                                params = []
                                args = []
                                for k, v in self.__mappings__.items():
                                    fields.append(v.name)
                                    params.append(getattr(self, k, None))
                                sql = 'insert into %s (%s) values (%s)' % (self.__table__, ','.join(foelds), ','.join(params))
                                print('SQL: %s' % sql)
                                print('ARGS: %s' % str(args))
                        ```
                - 输出如下
                    ```
                    Found model: User
                    Found mapping: email ==> <StringField:email>
                    Found mapping: password ==> <StringField:password>
                    Found mapping: id ==> <IntegerField:uid>
                    Found mapping: name ==> <StringField:username>
                    SQL: insert into User (password,email,username,id) values (?,?,?,?)
                    ARGS: ['my-pwd', 'test@orm.org', 'Michael', 12345]
                    ```
- 错误, 调试和测试
    - 错误处理
        - 内置: 高级语言一般都内置了一套`try...except...finally...`的错误处理机制, Python也不例外.
        - try: 
            ```
            try:
                print('try...')
                r = 10/0
                print('result:', r)
            except ZeroDivisionError as e:
                print('except:', e)
            finally:
                print('finally...')
            print('END')
            ```
        如果执行出错, 则后续代码不会继续执行, 而是直接跳转至错误处理代码, 即`except`语句块. 如果有`finally`语句块, 则执行`finally`语句块, 至此, 执行完毕.       
        上面的代码在计算`10/0`时会产生一个除法运算错误:
            ```
            try...
            except: division by zero
            finally...
            END
            ```
        如果把除数`0`改为`2`, 则执行结果如下:
            ```
            try...
            result: 5
            finally...
            END
            ```
        - 错误类型: 错误类型有很多种, 可以用多个`except`来捕获:
            ```
            except ValueError as e
            except ZeroDivisionError as e
            ```
        - else: 可以在`except`语句后面加一个`else`, 当没有错误发生时, 会自动执行`else`语句:
            ```
            try:
                pass
            except ZeroDivisionError as e:
                pass
            else:
                print('no error')
            finally:
                pass
            print('END')
            ```
        - 跨多层调用: 不需要在每个可能出错的地方捕获错误, 只要在合适的层次去捕获错误就可以了
        - 调用栈
            - 如果错误没有被捕获, 它就会向上一层抛, 最后被Python解释器捕获, 打印一个错误信息, 然后程序退出. 例如err.py:
                ```
                def foo(s):
                    return 10 / int(s)
                def bar(s):
                    return foo(s) * 2
                def main():
                    bar('0')
                main()
                ```
                执行, 结果如下:
                ```
                $ python err.py
                Traceback (most recent call last):
                    File "err.py", line 11, in<module>
                        main()
                    File "err.py", line 9, in main
                        bar('0')
                    File "err.py", line 6, in bar
                        return foo(s) * 2
                    File "err.py", line 3, in foo
                        return 10 / int(s)
                    ZeroDivisionError: division by zero
                ```
                解读如下:       
                我们从上往下可以看出整个错误的调用函数链:
                错误信息第一行:
                ```
                TrackBack (most recent call last):
                ```
                告诉我们这是错误的跟踪信息
                第2-3行:
                ```
                File "err.py", line 11, in <module>
                    main()
                ```
                调用main()出错了, 在代码文件`err.py`的第11行代码, 但原因在第9行:
                ```
                File "err.py", line 9, in main
                    bar('0')
                ```
                调用`bar('0')`出错了, 在文件的第9行, 但原因是第6行:
                ```
                File "err.py", line 6, in bar
                    return foo(s) = 2
                ```
                `return foo(s) = 2`这个语句错了, 但这不是最终原因, 继续往下看:
                ```
                File "err.py", line 3, in foo
                    return 10 / int(s)
                ```
                `return 10 / int(s)`这个语句错了, 这是错误产生的源头, 因为在下面打印出了:
                ```
                ZeroaDivisionError: integer division or modulo by zero
                ```
                根据错误类型`ZeroaDivisionError`, 我们判断, `int(s)`本身并没有错, 但是`int(s)`返回`0`, 在计算`10 / 0`时出错, 至此, 找到错误源头
                - 注意: 出错的时候, 一定要分析错误的调用栈信息, 才能定位错误的位置
        - 记录错误
            - 原因: 如果不捕获错误, 而让Python解释器来打印错误堆栈, 这样会导致整个程序被结束. 而我们捕获错误, 就可以把错误打印出来, 并分析原因, 同时让程序继续执行下去
            - Python内置的`logging`模块可以非常容易地记录错误信息:
            ```
            # err_logging.py

            import logging

            def foo(s):
                return 10 / int(s)

            def bar(s):
                return foo(s) * 2

            def main():
                try:
                    bar('0')
                except Exception as e:
                    logging.exception(e)

            main()
            print('END')
            ```
            同样是出错, 但程序打印完错误信息后会继续执行, 并正常退出:
            ```
            $ python3 err_logging.py
            ERROR:root:division by zero
            Traceback (most recent call last):
            File "err_logging.py", line 13, in main
                bar('0')
            File "err_logging.py", line 9, in bar
                return foo(s) * 2
            File "err_logging.py", line 6, in foo
                return 10 / int(s)
            ZeroDivisionError: division by zero
            END
            ```
            但打印完错误信息会, 程序还会正常执行: `print('END')`, 正常退出
            通过配置, `logging`还可以把错误记录到日志文件里, 方便事后排查.
        - 抛出错误
            - 与捕获错误的区别
                - 捕获错误: 是在无法判断代码块是否有错误时使用的, 如`try...catch...finally`. 是被动的. `捕获后程序可以继续运行`
                - 抛出错误: 确定错误发生的地方或情况. 是主动的, `抛出错误程序不会继续运行`
                - 总结: 抛出错误需和捕获错误一起运行
            - 解释: 因为错误是class, 捕获一个错误就是捕获到该class的一个实例. 因此, 错误并不是凭空产生的, 而是有意创建并抛出的. Python的内置函数会抛出很多类型的错误, 我们自己编写的函数也可以抛出错误.      
            如果要抛出错误, 首先根据需要, 可以定义一个错误的class, 选择好继承关系, 然后, 用`raise`语句抛出一个错误的实例:
            ```
            class FooEroor(ValueError):
                pass

            def foo(s):
                n = int(s)
                if n==0:
                    raise FooEroor('invalid value: %s' % s)
                return 10 / n

            foo('0')
            ```
            执行, 可以最后最终到自己定义的错误:
            ```
            $ python3 err_raise.py 
            Traceback (most recent call last):
            File "err_throw.py", line 11, in <module>
                foo('0')
            File "err_throw.py", line 8, in foo
                raise FooError('invalid value: %s' % s)
            __main__.FooError: invalid value: 0
            ```
            只有在必要的时候才定义我们自己的错误类型. 如果可以选择Python已有的内置的错误类型(比如`ValueError`, `TypeError`), 尽量使用Python内置的错误类型.       
            - 另一种错误处理的方式:
            ```
            def foo(s):
                n = int(s)
                if n==0:
                    raise ValueError('invalid value: %s' % s)
                return 10 / n
            def bar()
                try:
                    foo('0')
                except ValueError as e:
                    print('ValueError!')
                    raise
            bar()
            ```
            `raise`会将错误往上一层抛        
            - 疑问: 我们在`bar`函数中捕获了一个错误, 但是打印一个`ValueError!`后, 又把错误通过`raise`语句抛出 为何要多此一举呢? 
                - 解答: 并非多此一举, 反而相当常见. 捕获错误目的只是记录一下以便后续追踪. 但由于当前函数不知道应该怎么处理该错误, 所以, 最恰当的方式是继续往上抛, 让顶层调用者去处理
            - 不带参数的`raise`
                - 如果`raise`语句不带参数, 就会把当前错误原样抛出. 此外, 在`except`中`raise`一个错误, 可以把一种类型的错误转化成另一种类型:
                    ```
    - 调试
        - logging: 例:
            ```
            import logging

            s = '0'
            n = int(s)
            logging.info('n = %d' % n)
            print(10/n)
            ```
            `logging.info()`应该能输出一段文本. 运行时发现:
            ```
            Traceback (most recent call last):
            File "test.py", line 7, in <module>
                print(10/n)
            ZeroDivisionError: division by zero
            ```
            并未有`logging.info()`输出的文本. 这是为什么?        
            原因: 在`import logging`之后添加一行配置`logging.basicConfig(level=logging.INFO)`再试试:
            ```
            INFO:root:n = 0
            Traceback (most recent call last):
            File "test.py", line 7, in <module>
                print(10/n)
            ZeroDivisionError: division by zero
            ```
            `logging`允许我们制定记录信息的级别, 有`debug`/`info`/`warning`/`error`等几个级别, 指定高级别时, 低级别的`logging`输出不起作用. 注意配置级别时使用大写, 如`INFO`/`ERROR`
    - 单元测试
        - `__name__ == '__main__'`
            - `__name__`: 此模块名, 当此模块被运行而非调用时变为`__main()__`
            - `__main__`: 当模块被直接运行时模块名
            - 解释: 当模块被直接运行时, 以下代码将被运行, 当模块被导入时, 代码块不被运行
        - `unittest.main()`
            - 解释: 使用它可以方便的将一个单元测试模块变为可直接运行的测试脚本, main()方法使用TestLoader类来搜索所有包含在该模块中以"test"命名开头的测试方法, 并自动执行它们. 执行方法的默认顺序是: 根据ASCII码的顺序加载测试用例.
        - 命令行参数`-m unittest`
            - 可以通过`-m unittest`直接运行单元测试
                ```
                $ python -m unittest mydict_test
                .....
                ----------------------------------------------------------------------
                Ran 5 tests in 0.000s

                OK
                ```
            - 这是推荐的做法, 这样可以一次批量运行很多单元测试, 并且有很多工具可以自动来运行这些单元测试.
        - `setUp`和`tearDown`
            - 可以在单元测试中编写两个特殊的`setUp()`和`tearDown()`方法. 这两个方法会分别在每调用一个测试方法的前后被执行
            - 作用: setUp()和tearDown()方法有什么用呢？设想你的测试需要启动一个数据库，这时，就可以在setUp()方法中连接数据库，在tearDown()方法中关闭数据库，这样，不必在每个测试方法中重复相同的代码：
            ```
            class TestDict(unittest.TestCase):

                def setUp(self):
                    print('setUp...')

                def tearDown(self):
                    print('tearDown...')
            ```
            可以再次运行测试看看每个测试方法调用前后是否会打印出setUp...和tearDown..
        - 练习
            - 题目: 对Student类编写单元测试，结果发现测试不通过，请修改Student类，让测试通过
                ```
                Student.py

                class Student(object):
                    def __init__(self, name, score):
                        self.name = name
                        self.score = score
                    def get_grade(self):
                        if self.score < 0:
                            raise ValueError('%s less than zero')
                        if self.score > 100:
                            raise ValueError('%s greater than one hundred')
                        if self.score >= 60 and self.score < 80:
                            return 'B'
                        if self.score >= 80:
                            return 'A'
                        return 'C'
                ```
                测试模块:
                ```
                Student_test.py

                import unittest
                from mydict import Student

                class TestStudent(unittest.TestCase):

                    def test_80_to_100(self):
                        s1 = Student('Bart', 80)
                        s2 = Student('Lisa', 100)
                        self.assertEqual(s1.get_grade(), 'A')
                        self.assertEqual(s2.get_grade(), 'A')

                    def test_60_to_80(self):
                        s1 = Student('Bart', 60)
                        s2 = Student('Lisa', 79)
                        self.assertEqual(s1.get_grade(), 'B')
                        self.assertEqual(s2.get_grade(), 'B')

                    def test_0_to_60(self):
                        s1 = Student('Bart', 0)
                        s2 = Student('Lisa', 59)
                        self.assertEqual(s1.get_grade(), 'C')
                        self.assertEqual(s2.get_grade(), 'C')

                    def test_invalid(self):
                        s1 = Student('Bart', -1)
                        s2 = Student('Lisa', 101)
                        with self.assertRaises(ValueError):
                            s1.get_grade()
                        with self.assertRaises(ValueError):
                            s2.get_grade()

                if __name__ == '__main__':
                    unittest.main()
                ```
            - 答案:
                ```
                class Student(object):
                    def __init__(self, name, score):
                        self.name = name
                        self.score = score
                    def get_grade(self):
                        if self.score < 0:
                            raise ValueError('%s less than zero')
                        if self.score > 100:
                            raise ValueError('%s greater than one hundred')
                        if self.score >= 60 and self.score < 80:
                            return 'B'
                        if self.score >= 80:
                            return 'A'
                        return 'C'
                ```
    - 文档测试
        - Python内置的`doctest`模块可以直接提取注释中的代码并执行测试
        - 例子如下:
            ```
            # mydict2.py
            class Dict(dict):
                '''
                Simple dict but also support access as x.y style.

                >>> d1 = Dict()
                >>> d1['x'] = 100
                >>> d1.x
                100
                >>> d1.y = 200
                >>> d1['y']
                200
                >>> d2 = Dict(a=1, b=2, c='3')
                >>> d2.c
                '3'
                >>> d2['empty']
                Traceback (most recent call last):
                    ...
                KeyError: 'empty'
                >>> d2.empty
                Traceback (most recent call last):
                    ...
                AttributeError: 'Dict' object has no attribute 'empty'
                '''
                def __init__(self, **kw):
                    super(Dict, self).__init__(**kw)

                def __getattr__(self, key):
                    try:
                        return self[key]
                    except KeyError:
                        raise AttributeError(r"'Dict' object has no attribute '%s'" % key)

                def __setattr__(self, key, value):
                    self[key] = value

            if __name__=='__main__':
                import doctest
                doctest.testmod()
            ```
            运行:
            ```
            $ python mydict2.py
            ```
            什么输出都没有, 这是我们编写的文档测试的正确反应. 如果程序有问题, 比如把`__getattr__()`方法注释掉, 再运行就会报错:
            ```
            **********************************************************************
            File "mydict.py", line 8, in __main__.Dict
            Failed example:
                d1.x
            Exception raised:
                Traceback (most recent call last):
                File "C:\Users\1nfinity\AppData\Local\Programs\Python\Python36\lib\doctest.py", line 1330, in __run
                    compileflags, 1), test.globs)
                File "<doctest __main__.Dict[2]>", line 1, in <module>
                    d1.x
                AttributeError: 'Dict' object has no attribute 'x'
            **********************************************************************
            File "mydict.py", line 14, in __main__.Dict
            Failed example:
                d2.c
            Exception raised:
                Traceback (most recent call last):
                File "C:\Users\1nfinity\AppData\Local\Programs\Python\Python36\lib\doctest.py", line 1330, in __run
                    compileflags, 1), test.globs)
                File "<doctest __main__.Dict[6]>", line 1, in <module>
                    d2.c
                AttributeError: 'Dict' object has no attribute 'c'
            **********************************************************************
            1 items had failures:
            2 of   9 in __main__.Dict
            ***Test Failed*** 2 failures.
            ```
        - 测试异常时的输出: 会用`...`表示中间一大段烦人的输出
        - 作用: doctest非常有用, 不但可以用来测试, 还可以直接作为示例代码. 通过某些文档生成工具, 就可以自动把包含doctest的注释提取出来. 用户看文档时, 同时也看到了doctest.
- IO编程
    - 同步IO和异步IO: 本章主要讲同步IO, 异步IO太复杂, 涉及到通知问题, 等讲到服务器端程序开发时我们再讨论
        - 同步IO: CPU等待磁盘读写完继续运行
        - 异步IO: CPU不等待, 立即执行下面的命令
    - 文件读写
        - 背景介绍: 在磁盘上读写文件的功能是由操作系统提供的, 现代的操作系统不运行普通的程序直接操作磁盘.
            因此, 打开文件就是请求操作系统打开一个文件对象, 然后, 通过操作系统提供的接口从这个文件对象中读取数据, 或者把数据写入这个文件对象
        - 打开文件: 使用Python内置的open()函数, 传入文件名和标示符:
            ```
            >>> f = open('/Users/1nfinity/test.txt', 'r')
            ```
            `r`为标示符, 表示读, 这样就打开了文件
            - 文件不存在: `open()`会抛出一个`IOError`的错误, 并且给出错误码的详细的信息高速我们文件不存在
                ```
                >>> f=open('/Users/michael/notfound.txt', 'r')
                Traceback (most recent call last):
                File "<stdin>", line 1, in <module>
                FileNotFoundError: [Errno 2] No such file or directory: '/Users/michael/notfound.txt'
                ```
        - 读文件
            - `f.read()`方法: 可以一次读取文件的全部内容, Python将内容读到内存, 用一个str对象表示
            - `f.close()`: 文件对象会占用操作系统的资源, 且同时能打开的文件数量是有限的. 因此文件使用完毕后必须关闭
            - `with`语句: 由于文件读写时都有可能产生`IOError`, 而出错后`f.closed()`就不会被调用, 文件就不会正确关闭. 因此, 为了保证无论是否出错都能正确地关闭文件, 我们可以使用`try...finally`来实现.       
                这样写太繁琐, Python引入`with`语句来自动帮我们调用`close()`方法:
                ```
                with open('/path/to/file', 'r') as f:
                    print(f.read())
                ```
                效果和`try...finally`一样, 但更简洁, 且不必调用`f.closed()`方法.
            - `f.read(size)`: `read()`会调用文件所有内容, 如果文件过大, 内存就爆了. 为保险起见, 使用`read(size)`, 每次最多读取size个字节内容. 
            - `f.readline()`: 一次读取一行内容
            - `f.readlines()`: 一次读取所有内容并按行返回`list`. 因此, 如果是配置文件, 调用`readlines()`最方便
        - file-like-Object
            - 可能的类: file/内存的字节流/网络流/自定义流等
            - 原则: 不要求从特定类继承, 只要写个`read()`方法就行
            - 例子: `StringIO`就是在内存中创建的file-like-Object, 常用作临时缓冲.
        - 二进制文件
            - 方法: 使用`open()`, 参数为`rb`. 例:
                ```
                >>> f = open('/Users/michael/test.jpg', 'rb')
                >>> f.read()
                b'\xff\xd8\xff\xe1\x00\x18Exif\x00\x00...' # 十六进制表示的字节
                ```
        - 字符编码
            - `encoding`参数: 要读取非UTF-8编码的文本文件, 需要给`open()`函数传入`encoding`参数, 例如:
                ```
                >>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk')
                >>> f.read()
                '测试'
                ```
            - `UnicodeDecodeError`: 文本文件中可能夹杂着非法编码的字符. 因此, 还接收一个`error`参数, 表示如果遇到编码错误后如何处理. 比如直接忽略:
                ```
                >>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk', errors='ignore')
                ```
            - 写文件
                - 传入`open()`函数的标识符`w`或者`wb`
                - `close()`: 当我们写文件时, 操作系统旺旺不会立刻把数据写进磁盘, 而是放到内存缓存起来, 空闲的时候再慢慢写入. 只有调用`close()`方法时, 操作系统才保住把没有写入的数据全部写入磁盘.
                    - 忘记的后果: 只写了一部分到磁盘, 剩下的丢失了. 
                - `with`方法: 最保险
                    ```
                    with open('/Users/michael/test.txt', 'w') as f:
                    f.write('Hello, world!')    
                    ```
                    要写入特定编码的文本文件, 请给`open()`函数传入`encoding`参数, 将字符串自动转换成指定编码
        - 使用`with`语句操作文件IO是一个好习惯
    - StringIO和BytesIO
        - StringIO: 
            - 作用: 从内存中读写str
            - 例子:
                ```
                >>> from io import StringIO
                >>> f = StringIO()
                >>> f.write('hello')
                5
                >>> f.write(' ')
                1
                >>> f.write('world!')
                6
                >>> print(f.getvalue())
                hello world!
                ```
                `getvalue()`用于获得写入后的str
            - 读取`StringIO`
                ```
                >>> from io import StringIO
                >>> f = StringIO('Hello!\nHi!\nGoodbye!')
                >>> while True:
                ...     s = f.readline()
                ...     if s == '':
                ...         break
                ...     print(s.strip())
                ...
                Hello!
                Hi!
                Goodbye!
                ```
        - BytesIO
            - 作用: 操作二进制数据. 例:
                ```
                >>> from io import BytesIO
                >>> f = BytesIO()
                >>> f.write('中文'.encode('utf-8'))
                6
                >>> print(f.getvalue())
                b'\xe4\xb8\xad\xe6\x96\x87'
                ```
                注意: 写入的不是str, 而是经过UTF-8编码的bytes         
                英文字符输出的还是英文字符
    - 操作文件和目录
        - `os`模块: 直接调用操作系统的接口
            - `os.name`: 显示操作系统类型
            - `os.uname()`: 显示系统详细信息, 在Windows上不提供
            - 环境变量
                - `os.environ`: 查看环境变量
                - `os.environ.get('key)`
        - 操作文件和目录
            - 目录操作
                - 查看目录: 
                    ```
                    >>> os.path.abspath('.')
                    '/Users/michael'
                    >>> os.path.join('/Users/michael', 'testdir')
                    '/Users/michael/testdir'
                    >>> os.mkdir('/Users/michael/testdir')
                    # 删掉一个目录
                    >>> os.rmdir('/Users/michael/testdir')
                    ```
                - `os.path.join()`: 将两个路径合成一个
                    - 好处: 可以正确处理不同操作系统的路径分隔符
                - `os.path.split()`: 拆分路径. 后一部分总是最后级别的目录或文件名:
                    ```
                    >>> os.path.split('.Users/michael/testdir/file.txt')
                    ('/Users/michael/testdir', 'file.txt')
                    ```
                - `os.path.splitext()`: 直接得到文件扩展名
                - 这些合并/拆分函数并不要求目录和文件真实存在, 它们只对字符串进行操作
            - 文件操作
                - `os.rename()`: 重命名文件: `os.rename('test.txt', 'test.py')
                - `os.remove()`: 删除文件: `os.remove('test.py')`
                - 没有复制模块:
                    - `os`未提供
                    - `shutil`模块提供:
                        - `copyfile()`
                        - 可以看做是`os`模块的补充, 还有很多其它实用函数
                - 过滤文件
                    - 列出当前目录下的所有目录:
                        ```
                        >>> [x for x in os.listdir('.') if os.path.isdir(x)]
                        ```
                    - 列出所有`.py`文件
                        >>> [x for x in os.listdir('.') if os.path.isfile(x) and os.path.splitext(x)[1]=='.py']
                        ```
            - 小结: python的`os`模块封装了操作系统的目录和文件操作, 要注意这些函数有的在`os`模块中, 有的在`os.path`模块中.
    - 序列化
        - 定义: 变量从内存中变成可储存对象或传输的过程称之为序列化, 在Python中叫pickling, 在其它语言中叫serialization, marshaling,flattening等等, 都是一个意思
        - 反序列化: 把变量内容从序列化的对象重新读到内存里称之为反序列化, 即unpickling           
        - 目的: 
            1. 以某种存储形式使自定义对象持久化；
            2. 将对象从一个地方传递到另一个地方。
            3. 使程序更具维护性。
        - 例子: 
            - 将数据转化成xml/json
            - 将数据转化成文件/流
        - `pickle`模块
            - 作用: 实现序列化
            - `pickle.dumps()`: 把任意对象序列化成一个`bytes`. 例:
                ```
                >>> import pickle
                >>> d = dict(name='Bob', age=20, score=88)
                >>> pickle.dumps(d)
                b'\x80\x03}q\x00(X\x03\x00\x00\x00ageq\x01K\x14X\x05\x00\x00\x00scoreq\x02KXX\x04\x00\x00\x00nameq\x03X\x03\x00\x00\x00Bobq\x04u.'
                ```
            - `pickle.dump()`(**没有`s`**)可以把一个对象序列化后写入文件
                ```
                >>> f = open('dump.txt', 'wb')
                >>> pickle.dump(d, f)
                >>> f.close()
                ```
            - `pickle.loads()`: 反序列化对象
            - `pickle.load()`: 从一个`file-like-Object`中直接反序列化对象
        - `json`模块: Python对象到JSON格式的转换
            - `json.dumps()`: 将对象变成一个`json`样式的`str`. 
            - `json.dump()`: 将`json`写入一个`file-like-Object`
            - `json.loads()`: 把`json`的字符串反序列化
            - `json.load()`: 从`file-like-Object`中读取字符串并反序列化
        - JSON进阶
            - 将`class`序列化为`json`
                - 直接转化: 会报错, 因为`class`的实例不是一个可序列化为JSON的对象
                - 间接转化
                    - `dumps()`提供一大堆可选参数
                        - 可选参数`default`: 把任意一个对象变成一个可序列为JSON的对象, 我们只需要写一个转换函数, 再把函数穿进去即可:
                            - 将特定`class`实例转化成JSON
                                ```
                                def student2dict(std):
                                    return {
                                        'name': std.name,
                                        'age': std.age,
                                        'score': std.score
                                    }
                                ```
                                `class`的实例`Student`首先被`student2dict()`转换成`dict`, 再被顺利序列化为JSON
                            - 将任意`class`的实例变成`json`
                                ```
                                print(json.dumps(s, default=lambda obj: obj.__dict__))
                                ```
                                其中`__dict__`属性是一个`dict`, 用来储存实例变量
            - 将`json`反序列化为`class`
                - 先用`loads()`方法将`json`转换成一个`dict`对象, 然后, 我们传入的`object_hook`函数负责将`dict`转换成`Student`实例:
                    ```
                    def dict2student(d):
                        return Student(d['name'], d['age'], d['score']) 
                    ```
                    运行结果如下:
                    ```
                    >>> json_str = '{"age": 20, "score": 88, "name": "Bob"}'
                    >>> print(json.loads(json_str, object_hook=dict2student))
                    <__main__.Student object at 0x10cd3c190>
                    ```
        - 练习
            - 题目: 对中文进行JSON序列化时, `json.dumps()`提供了一个`ensure_ascii`参数, 观察该参数对结果的影响:
- 进程和线程
    - 总览
        - 通俗理解: 多进程和多线程就是操作系统让各个任务(进程)轮流执行, 让各个任务里面的子任务(线程)轮流执行
        - 任务之间的联系: 同时执行多个任务通常各个任务之间并不是内有关联的, 而是需要相互通信和协调, 有时, 任务1必须暂停等待任务2完成后才能继续执行, 有时, 任务3和任务4又不能同时执行, 所以, 多进程和多线程的程序的复杂度要远远高于我们前面写的单进程单线程的程序.
    - 多进程
        - `fork()`: Unix/Linux专属
            - 调用一次, 返回两次. 操作系统自动把当前进程(称为父进程)复制一份(称为子进程), 然后, 分别在父进程和子进程中返回
            - 返回值: 子进程永远返回`0`, 父进程返回子进程的pid. 这样做的理由: 一个父进程可以fork出多个子进程, 所以, 父进程要记下每个子进程的ID, 而子进程只需要调用`getppid()`就可以拿到父进程的ID
            - Python中的`fork`: Python的
        - `multiprocessing`模块
            - `Process`类: 代表一个进程对象. 下面的例子演示了启动一个子进程并等待其结束:
                ```
                from multiprocessing import Process
                import os

                # 子进程要执行的代码
                def run_proc(name):
                    print('Run child process %s (%s)...' % (name, os.getpid()))

                if __name__=='__main__':
                    print('Parent process %s.' % os.getpid())
                    p = Process(target=run_proc, args=('test',))
                    print('Child process will start.')
                    p.start()
                    p.join()
                    print('Child process end.')
                ```
                执行结果如下:
                ```
                Parent process 928.
                Process will start.
                Run child process test (929)...
                Process end.
                ```
                创建子进程时, 只需要传入一个执行函数和函数的参数, 创建一个`Process`实例, 这样创建进程比`fork()`简单       
                `join()`方法可以等待子进程结束后再继续往下运行, 通常用于进程间的同步.
        - `Pool`模块: 进程池, 启动大量子进程时使用
            ```
            from multiprocessing import Pool
            import os, time, random

            def long_time_task(name):
                print('Run task %s (%s)...' % (name, os.getpid()))
                start = time.time()
                time.sleep(random.random() * 1)
                end = time.time()
                print('Task %s runs %0.2f seconds' % (name, (end - start)))

            if __name__=='__main__':
                print('Parent process %s' % os.getpid())
                p = Pool(4)
                for i in range(5):
                    p.apply_async(long_time_task, args=(i,))
                print('Waiting for all subprocesses done...')
                p.close()
                p.join()
                print('All subprocesses done')
            ```
            p = Pool(4)意味着同时进行的子进程为4个, 也就是同时运行4个`long_time_task`函数
        - 子进程: 很多时候, 子进程不是自身, 而是一个外部进程. 我们创建了子进程后, 还要控制子进程的输入和输出.
            - `subprocess`模块: 可以让我们非常方便的启动一个子进程, 然后控制其输入和输出. 例:
                ```
                import subprocess

                print('$ nslookup www.python.com')
                r = subprocess.call(['nslookup', 'www.python.com'])
                print('Exit code:', r)
                ```
                运行结果
                ```
                $ nslookup www.python.org
                Server:        192.168.19.4
                Address:    192.168.19.4#53

                Non-authoritative answer:
                www.python.org    canonical name = python.map.fastly.net.
                Name:    python.map.fastly.net
                Address: 199.27.79.223

                Exit code: 0
                ```
                如果子进程还需要输入, 则可以通过`communicate()`方法输入:
                ```
                import subprocess

                print('$ nslookup')
                p = subprocess.Popen(['nslookup'], stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
                output, err = p.communicate(b'set q=mx\npython.org\nexit\n')
                print(output.decode('utf-8'))
                print('Exit code:', p.returncode)
                ```
                运行结果
                ```
                $ nslookup
                Server:        192.168.19.4
                Address:    192.168.19.4#53

                Non-authoritative answer:
                python.org    mail exchanger = 50 mail.python.org.

                Authoritative answers can be found from:
                mail.python.org    internet address = 82.94.164.166
                mail.python.org    has AAAA address 2001:888:2000:d::a6


                Exit code: 0
                ```
        - 进程间通信
            - Python的`multiprocessing`模块封装了操作系统的底层机制来实现`Process`之间的通信, 提供了Queue, Pipes等多种方式来交换数据.
                ```
                from multiprocessing import Process, Queue
                import os, time, random

                def write(q):
                    for value in ['A', 'B', 'C']:
                        print('Process to write: %s' % os.getpid()) 
                        print('Put %s to queue...' % value)
                        q.put(value)
                        time.sleep(random.random())
                    
                # 读数据进程执行的代码
                def read(q):
                    while True:
                        print('Process to read: %s' % os.getpid())
                        value = q.get(True)
                        print('Get %s from queue.' % value)

                if __name__ == '__main__':
                    q = Queue()
                    pw = Process(target=write, name=None, args=(q,) )
                    pr = Process(target=read, name=None, args=(q,) )
                    pw.start()
                    pr.start()
                    pw.join()
                    pr.terminate()
                ```
                运行结果
                ```
                Process to write: 50563
                Put A to queue...
                Process to read: 50564
                Get A from queue.
                Put B to queue...
                Python3 基础教程【完整版】 http://www.yeayee.com/
                274/531
                Get B from queue.
                Put C to queue...
                Get C from queue.
                ```
        - 小结
            - 在Unix/Linux下, 可以使用`fork()`来调用实现多进程.
            - 要实现跨平台的多进程, 可以使用`multiprocessing`模块.
            - 进程间通信是通过`Queue`/`Pipes`等实现的.
    - 多线程
        - 多任务可以由多进程完成, 也可以由一个进程内的多线程完成.
        - Python的标准库提供了两个模块: `_thread`和`threading`. `threading`是高级模块, 是对`_thread`模块的封装
        - 启动线程: 启动一个线程就是把一个函数传入并创建Thread实例, 然后调用`start()`开始执行:
            ```
            import time, threading

            def loop():
                print('thread %s is running...' % threading.current_thread().name)
                n = 0
                while n < 5:
                    n = n + 1
                    print('thread %s >>> %s' % (threading.current_thread().name, n))
                    time.sleep(1)
                print('thread %s ended.' % threading.current_thread().name)
            print('thread %s is running...' % threading.current_thread().name)
            t = threading.Thread(target=loop, name='LoopThreadT')
            t.start()
            t.join()
            print('thread %s ended. ' % threading.current_thread().name)
            ```
            执行结果: 
            ```
            thread MainThread is running...
            thread LoopThreadT is running...
            thread LoopThreadT >>> 1
            thread LoopThreadT >>> 2
            thread LoopThreadT >>> 3
            thread LoopThreadT >>> 4
            thread LoopThreadT >>> 5
            thread LoopThreadT ended.
            thread MainThread ended.
            ```
        - 任何进程默认会启动一个线程, 我们称之为主线程, 主线程可以启动新的线程. 主线程实例的名字叫`MainThread`. 子线程的名字在创建时指定
        - Lock
            - 多线程与多进程最大的不同: 多进程中, 同一个变量, 各自有一份拷贝存在于每个进程中, 互不影响. 而多线程中, 所有变量都由所有线程共享, 因此, 任何一个变量都可以被任何一个线程修改, 这样, 线程之间共享数据最大的危险在于多个进程同时改一个变量, 把内容改乱了. 让我们来看一个例子:
                ```
                import time, threading

                # 假定这是你的银行存款:
                balance = 0

                def change_it(n):
                    # 先存后取，结果应该为0:
                    global balance
                    balance = balance + n
                    balance = balance - n

                def run_thread(n):
                    for i in range(100000):
                        change_it(n)

                t1 = threading.Thread(target=run_thread, args=(5,))
                t2 = threading.Thread(target=run_thread, args=(8,))
                t1.start()
                t2.start()
                t1.join()
                t2.join()
                print(balance)
                ```
                上述代码运行的结果理论上应该是`0`, 但是, 由于线程的调度是操作系统决定的, 当t1, t2交替执行时, 只要循环次数足够多, `balance`的结果就不一定是`0`.         
                原因: 在高级语言中, `balance = balance + n`这条语句在CPU中执行时是分两条的:
                ```
                x = balance + n
                balance = x
                ```
                当t1和t2交替运行时, 如果操作系统以下面的顺序执行t1/t2:
                ```
                初始值 balance = 0
                t1: x1 = balance + n # x1 = 5
                t2: x2 = balance + n # x2 = 8
                t2: balance = x2 # balance = 8
                t1: balance = x1 # balance = 5

                结果: balance = 5
                ```
                由于修改`balance`需要多条语句, 而执行这几条语句时, 线程可能中断, 从而导致多个线程把同一个对象改乱了.
            - 当我们要确保`change_it(n)`的计算不出错时, 就要给`change_it()`一把锁, 由于锁只有一个, 无论多少线程, 同一时间只有一个线程可以持有该锁, 所以, 不会造成修改的冲突, 但这样会导致运行拥有锁的代码实际上是以单线程模式执行的. 创建一个锁就是通过`threading.Lock()`来实现:
                ```
                balance = 0
                lock = threading.Lock()

                def run_thread(n):
                    for i in range(100000):
                        # 先要获取锁:
                        lock.acquire()
                        try:
                            # 放心地改吧:
                            change_it(n)
                        finally:
                            # 改完了一定要释放锁:
                            lock.release()
                ```
            - 死锁: 由于可以存在多个锁, 不同的线程持有不同的锁, 并试图获取对方持有的锁时, 可能造成死锁, 导致多个线程挂起, 既不执行, 也无法结束, 只能靠操作系统强制终止.
        - 多核CPU
            - 默认
                - 一个死循环占用一个CPU
                - 两个死循环在多核CPU中, 会占用200%的CPU
            - Python中, 运行死循环, 在4核CPU上的占用率仅有102%
                - 原因: 因为Python的线程虽然是真正的线程，但解释器执行代码时，有一个GIL锁：Global Interpreter Lock，任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。
            - Python虽然不能利用多线程实现多核任务，但可以通过多进程实现多核任务。多个Python进程有各自独立的GIL锁，互不影响。
        - 小结
            - 多线程编程, 模型复杂, 容易发生冲突, 必须用锁加以隔离, 同时, 又要小心死锁的发生.
            - Python解释器由于设计时有GIL全局锁, 导致多线程无法利用多核. 
- Web开发
    - HTTP协议简介
        - Content-Type: Content-Type指示响应的内容，这里是text/html表示HTML网页。请注意，浏览器就是依靠Content-Type来判断响应的内容是网页还是图片，是视频还是音乐。浏览器并不靠URL来判断响应的内容，所以，即使URL是http://example.com/abc.jpg，它也不一定就是图片。

