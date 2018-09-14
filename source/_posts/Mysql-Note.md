---
title: Mysql-Note
date: 2018-01-17 16:20:03
tags:
category: Mysql
---
- Mysql can't create table error 150
    - 问题: 保存table时报错: MySQL: Can't create table '' (errno: 150)
    - 原因: ![参考](https://dev.mysql.com/doc/refman/5.7/en/create-table-foreign-keys.html), 必须要保证字段名/字段类型/字段的编码格式(特别注意, 可能一个是latin1, 一个是utf8)一致
- group by: 用于结合合计函数，根据一个或多个列对结果集进行分组。<!-- more -->
    - 例子:
        - statement: 
            ```
            select  buy_user, count(buy_user)
            from `order` 
            where `sale_vessel_id` = 1 
            and order_id like 'hg%' 
            and `status` = 'reject_pay'
            group by buy_user
            ```
        - resultMap:
            ```
            | buy_user   | count(buy_user)    |
            | --------:   | -----:   |
            | ran | 1|
            | tai | 1|
            | zou | 6|
            ```
    - performance difference between query 1: `insert into user (username, phone) values ("username", "phone");insert into user (username, phone) values ("username", "phone");insert into user (username, phone) values ("username", "phone");` and query 2:  `insert into user (username, phone) values ("username", "phone"), ("username", "phone"), ("username", "phone")`
        -explain:  every query in query 1 cost 0.003s, total query 2 cost 0.003s, so query 1 practical cost 0.003*n, so 2 is better than 1 in preformance. but query 1 is more flexible, b
- 将`tinyText`类型的字段设置索引为`unique`时报错: `key specification without a key length
    - 原因: 
            `The error happens because MySQL can index only the first N chars of a BLOB or TEXT column. So The error mainly happens when there is a field/column type of TEXT or BLOB or those belong to TEXT or BLOB types such as TINYBLOB, MEDIUMBLOB, LONGBLOB, TINYTEXT, MEDIUMTEXT, and LONGTEXT that you try to make a primary key or index. With full BLOB or TEXT without the length value, MySQL is unable to guarantee the uniqueness of the column as it’s of variable and dynamic size. So, when using BLOB or TEXT types as an index, the value of N must be supplied so that MySQL can determine the key length. However, MySQL doesn’t support a key length limit on TEXT or BLOB. TEXT(88) simply won’t work.`       
            `The solution to the problem is to remove the TEXT or BLOB column from the index or unique constraint or set another field as primary key. If you can't do that, and wanting to place a limit on the TEXT or BLOB column, try to use VARCHAR type and place a limit of length on it. By default, VARCHAR is limited to a maximum of 255 characters and its limit must be specified implicitly within a bracket right after its declaration, i.e VARCHAR(200) will limit it to 200 characters long only.`      
            　　简单来说, `mysql`只会索引`BLOB`或`TEXT`的前`N`个字符, 如果不加限制, 则会报错. 但`mysql`又不支持对`BLOB`或`TEXT`进行键长度限制, 因此无法使用`BLOB`或`TEXT`进行索引.     
            　　解决办法是: 将`BLOB`或`Text`换成`VARCHAR`类型, 并对其进行限制, 然后对`VARCHAR`类型进行索引．
- 将字段类型由`Text`改为`VARCAHR`时, 报错: `Data too long for column`
            - 原因: 需要对`VARCAHR`的长度进行限制