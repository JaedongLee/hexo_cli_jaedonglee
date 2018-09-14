---
title: Python问题解析-关于__getattr__的链式调用的应用的一点解析
date: 2017-11-23 18:08:01
tags:
Category: Python
---
- <span style="background-color: red>关于`__getattr__()`的链式调用的应用的一点解析</span>
    - 实例: 现在很多网站都搞REST API，比如新浪微博、豆瓣啥的，调用API的URL类似：

        http://api.server/user/friends<!-- more -->
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