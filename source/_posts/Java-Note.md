---
title: Java-Note
date: 2018-01-22 10:51:44
tags:
category: Java
---
- Spring
    - Spring Mybatis Mapper 报错
        - 问题: 调用dao类方法,如`userApplyMapper.updateByPrimaryKeySelective`时, 报空指针错误
        - 分析: 发现
            ```
            @Autowired
            private UserMapper userMapper;
            private UserApplyMapper userApplyMapper;
            ```
            第二个变量`userApplyMapper`是暗的, 显示`Private field 'userApplyMapper' is never assigned`. 因此推测: 一个`@Autowired`只能用于一个变量, 将代码改为:
            ```
            @Autowired
            private UserMapper userMapper;

            @Autowired
            private UserApplyMapper userApplyMapper;
            ```
            成功
        - 结论: `@Autowired`只能装配一个变量
        - 推论: 其它注释也只能装饰一个类或者方法, 这也是必然的.     <!-- more -->
    - Spring项目报错: 
        ```
        org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'userController': Injection of autowired dependencies failed; nested exception is org.springframework.beans.factory.BeanCreationException: Could not autowire field: private com.ylxx.custom.service.UserService com.ylxx.custom.controller.UserController.userService; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'userServiceImple': Injection of autowired dependencies failed; nested exception is org.springframework.beans.factory.BeanCreationException: Could not autowire field: private com.ylxx.custom.dao.UserMapper com.ylxx.custom.service.UserServiceImple.userMapper; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'userMapper' defined in file [E:\Develop\counter\server\counter\out\artifacts\counter\WEB-INF\classes\com\ylxx\custom\dao\UserMapper.class]: Cannot resolve reference to bean 'sessionFactory' while setting bean property 'sqlSessionFactory'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'sessionFactory' defined in class path resource [applicationContext.xml]: Invocation of init method failed; nested exception is org.apache.ibatis.type.TypeException: The alias 'UserRole' is already mapped to the value 'com.ylxx.custom.entity.UserRole'.
        ```
        - 分析: 看最后一句`The alias 'UserRole' is already mapped to the value 'com.ylxx.custom.entity.UserRole'.`, 意思是`UserRole`这个名称已经映射到`com.ylxx.custom.entity.UserRole`上了, 联想到项目中刚加过一个`enum`: `UserRole`. 将其换掉后, 项目成功运行.
        - 总结: 在Spring中, 一个项目里, Java文件的名称是唯一的, 如果文件名相同, 里面的对象的种类不同,如`Enum`和`Class`类. 即使编译没问题, 运行时也会报错
    - `==`与`equals()`的区别
        - 总结: `==`比较的是对象的引用, 即两个变量是否引用自同一个对象, 而`equals()`比较的是两个变量的定义, 如果定义相同, 它们就相同, 即使来自不同的对象, 例:
            - 20180201补充: `equals()`的实现方式就是`==`, 所以没区别, 但`equals()`可以被重写, 从而可以用来比较等价性
        ```
        class Foo {
            ...
        }

        Foo f1 = new Foo(5);
        Foo f2 = new Foo(5);
        System.out.println(f1 == f2);
        // outputs false, they're distinct object instance
        System.out.println(f1.equals(f2));
        // outputs trues, they're "equal" according to their definiition
        ```
        简单来说, `==`比较是否是同一个对象, `equals()`比较是否是同一类对象
    - spring 项目连接数据库时报错:
        ```
        HTTP Status 500 - Request processing failed; nested exception is org.springframework.transaction.CannotCreateTransactionException: Could not open JDBC Connection for transaction; nested exception is com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure
        ```
        - 原因: `Mysql`由于重启或者超时自动断开了连接, 而另一方不知道, 因此会继续使用错误的连接, 从而导致浏览器一直加载最后报错.
        - 解决方案: 
            1. 设置更长的wait_timeout, 这种方式很直接，就是MySQL会等待更长的时间，如果连接上没有数据传输，会关闭这个连接. 这种方式的缺点是如果您的应用可能超过wait_timeout都不使用数据库连接，那么在使用的时候还是有可能会出现相同的错误。
            2. 引用`c3p0`官方文档中的解释, 重点是最后两段:
                ```
                When configuring Connection testing, first try to minimize the cost of each test. If you are using a JDBC driver that you are certain supports the new(ish) jdbc4 API — and if you are using c3p0-0.9.5 or higher! — let your driver handle this for you. jdbc4 Connections include a method called isValid() that should be implemented as a fast, reliable Connection test. By default, c3p0 will use that method if it is present.

                However, if your driver does not support this new-ish API, c3p0's default behavior is to test Connections by calling the getTables() method on a Connection's associated DatabaseMetaData object. This has the advantage of being very robust and working with any database, regardless of the database schema. However, a call to DatabaseMetaData.getTables() is often much slower than a simple database query, and using this test may significantly impair your pool's performance.

                The simplest way to speed up Connection testing under a JDBC 3 driver (or a pre-0.9.5 version of c3p0) is to define a test query with the preferredTestQuery parameter. Be careful, however. Setting preferredTestQuery will lead to errors as Connection tests fail if the query target table does not exist in your database prior to initialization of your DataSource. Depending on your database and JDBC driver, a table-independent query like SELECT 1 may (or may not) be sufficient to verify the Connection. If a table-independent query is not sufficient, instead of preferredTestQuery, you can set the parameter automaticTestTable. Using the name you provide, c3p0 will create an empty table, and make a simple query against it to test the database.

                The most reliable time to test Connections is on check-out. But this is also the most costly choice from a client-performance perspective. Most applications should work quite reliably using a combination of idleConnectionTestPeriod and testConnectionOnCheckin. Both the idle test and the check-in test are performed asynchronously, which can lead to better performance, both perceived and actual.
                ```
                总结下来就是: `c3p0`版本在0.95以下, 推荐在`applicationContext.xml`中使用以下配置:
                ```
                <property name="preferredTestQuery" value="select 1"></property>
                <property name="testConnectionOnCheckout" value="false"></property>
                <property name="testConnectionOnCheckin" value="true"></property>
                <property name="idleConnectionTestPeriod" value="30"></property>
                ```
- Mybatis
    - 浏览器报错: `java.lang.IllegalArgumentException: No enum constant org.apache.ibatis.type.JdbcType.STRING`
        - 原因: Mybatis中的`jdbcType`没有`String`数据类型, 只有`VARCHAR`, 将其改为`VARCHAR`, 运行成功
        - 推论: 如果改掉一个此错误后, 还有报这种错误, 这意味着还有一个地方有这种错误.
    - 报错: `Parameter 'XXX' not found. Available parameters are [collection, list]`
        - 原因: 在`XXXMapper.java`类中, 需要添加`@Param("XXX)`, 其中`XXX`与`collection`中的`value`一样, 例:
        ```
        @XXX.xml
        <foreach collection="orderIdList" index="index" item="item" separator=",">

        @xxxMapper.java
        List<OrderItemHistory> selectByOrderIdList(@Param("orderIdList") List<String> orderIdList);

        ```
- Java
    - public String substring(int startIndex, int endIndex)中的 endIndex 不包含自身, 即 substring(0, 2) 只含有一个字符
