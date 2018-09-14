---
title: Java-Note-Mybatis-SqlSessionFactory和SqlSession
date: 2018-04-16 09:46:03
tags: Mybatis
category: Java
---
- SqlSessionFactory: 是Mybatis的关键对象, 它是单个数据库映射关系经过编译后的内存镜像. SqlSessionFactory对象的实例可以通过SqlSessionFactoryBuilder对象类获得, 而SqlSessionFactoryBuilder则可以从XML配置文件或一个预先定制的Configuration的实例构建出SqlSessionFactory的实例, 每一个Mybatis的应用程序都以一个SqlSessionFactory对象的实例为核心, 同时SqlSessionFactory也是线程安全的, SqlSessionFactory是创建SqlSession的工厂
- SqlSession: 是MyBatis的关键对象, 是执行持久化操作的独享, SqlSession对象完全包含以数据库为背景的所有执行SQL操作的方法, 其底层封装了JDBC连接, 可以用SqlSession实例来直接执行被映射的SQL语句.<!-- more -->
- SqlSessionFactory和SqlSession的实现过程
    1. 定义一个Configuration对象, 其中包括数据源/事务/mapper文件资源及影响数据库行为属性的设置
    2. 通过Configuration对象, 则可以创建一个SqlSessionFactoryBuilder对象
    3. 通过SqlSessionFactoryBuilder获得SqlSessionFactory实例
    4. SqlSessionFactory的实例可以获得操作数据的SqlSession实例, 通过这个实例对数据库进行操作
- 连接池配置: 在applicationContext中进行配置. 设置dataSource的时候, 可以配置PooledDataSource, 这样可以使用连接池
    - 使用连接池原因: 因为创建一个Connection对象的过程，在底层就相当于和数据库建立的通信连接，在建立通信连接的过程，消耗了非常多的时间，而往往我们建立连接后（即创建Connection对象后），就执行一个简单的SQL语句，然后就要抛弃掉，这是一个非常大的资源浪费
- 调用具体语句:
    - SqlSessionTemplate中的内部类SqlSessionInterceptor中的invoke方法的参数args会包含Dao中方法的全限定名
    - Configuration这个类会在初始化时根据applicationContext中的配置把mapper里面的所有的xml文件全部加载进一个mappedStatements Map里面, 然后DefaultSqlSession会根据全限定名去获取对应的mappedStatement