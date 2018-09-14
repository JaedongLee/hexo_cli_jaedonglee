---
title: JavaScript-Note.md
date: 2017-11-22 16:32:29
tags:
category: JavaScript
---
- 原型链
    - 构造实例和定义方法的先后问题
        - 代码如下
            ```
            function Person () {
                this.name = 'John';
            }
            var person = new Person();
            Person.prototype.say = function() {
                console.log('Hello,' + this.name);
            };
            person.say();//Hello,John
            ```
            虽然原型方法在构造实例之后声明, 但因为在调用方法之前就已经声明了, 因此之后的每个实例都拥有该方法.      
            如果我们这样写:
            ```
            function Person () {
                this.name = 'John';
            }
            var person = new Person();
            Person.prototype = {
                say: function() {
                    console.log('Hello,' + this.name);
                }
            };
            person.say();//person.say is not a function
            ```
            person.say方法没找到, 报错.        
            原因: `var person = new Person()`创建了一个新实例, 即Person.prototype指向了一个空对象. 对于实例person来说, 其内部有一个原型链指针proto, 该指针指向了Person.prototype指向的对象.而接下来:
            ```
            Person.prototype = {
                say: function() {
                    console.log('Hello,' + this.name);
                }
            };
            ```
            则将Person.prototype重置了, 使其指向了另一个对象即: `Object(say: function)`     
            而此时person.proto的指向没有变, 其指向的空对象没有`say`方法, 因此报错.      
            想要成功运行, 则需将方法定义放在构造实例之前:
            ```
            function Person () {
                this.name = 'John';
            }
            Person.prototype = {
                say: function() {
                    console.log('Hello,' + this.name);
                }
            };
            var person = new Person();
            person.say();
            ```
            其实, 只需要明白原型对象的结构即可:
            ```
            Function.prototype = {
                constructor : Function,
                __proto__ : parent prototype,
                some prototype properties: ...
            };
            ```
        - 构造函数格式: 为了区分普通函数和构造函数, 按照约定, 构造函数首字母应当大写, 而普通函数首字母应当小写
- Ajax cross domain session change problem and solution
    - 解释: 使用`ajax`跨域请求服务器会出现`sessionId`变化的问题, 即每次连接都会产生一个新的sessionId<!-- more -->
    - 原因: 根据浏览器的保护规则, 跨域的时候我们创建的sessionId是不会被浏览器保存下来, 即每一次请求, 服务器都会以为是一个新的人(客户端)
    - 解决方式: 
        - 前端添加:
        ```
        $.ajax({
            type: "POST",
            url: "http://192.168.0.113:8081/user/login.do",
            data: orderData,
            contentType: "application/json",
            cache: false,
            <!-- 额外添加 -->
            xhrFields: {
                withCredentials: true
            },
            crossDomain: true,
            <!-- 额外添加 -->
            processData: false,
            success: function (data) {
                console.log(data);
            },
            error: function (data) {
                console.log(data);
            }
        })

        ```
        - 后台添加:
        ```
        public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
            HttpServletRequest request = (HttpServletRequest) servletRequest;
            HttpServletResponse response = (HttpServletResponse) servletResponse;
            response.addHeader("Access-Control-Allow-Origin", request.getHeader("Origin"));
            response.setHeader("Access-Control-Max-Age", "3600");
            response.addHeader("Access-Control-Allow-Headers", "*");
            <!-- 额外添加 -->
            response.setHeader("Access-Control-Allow-Credentials", "true");
            response.setHeader("XDomainRequestAllowed","1");
            <!-- 额外添加 -->
            filterChain.doFilter(servletRequest, servletResponse);
        }
        ```
    - 参考文档: https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS