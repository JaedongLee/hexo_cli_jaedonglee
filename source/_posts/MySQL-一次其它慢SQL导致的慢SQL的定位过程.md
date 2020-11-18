---
title: MySQL-一次其它慢SQL导致的慢SQL的定位过程
date: 2020-09-28 09:54:04
tags:
---

### 分析过程

1. 使用 StopWatch 统计接口各子方法耗时:

   发现 dao 层方法  storeCustomerMapper.getStoreCustomerInfoList 耗时达 27 秒

   ![](http://note.youdao.com/yws/public/resource/340ba176925ee287a13ec8321f3181f3/xmlnote/FA034CC7940742A4999BB0F22DF7630D/21792)

2. 检查慢 sql 日志该方法用到的 sql, 发现其耗时 27 秒

   ![](http://note.youdao.com/yws/public/resource/340ba176925ee287a13ec8321f3181f3/xmlnote/4482D76EF8E9493CBC6B6001932D97A2/21794)

3. 该 sql 单独执行很快, 因此猜测是该 sql 执行时, 有其它大 sql 正在执行, 导致该 sql 执行缓慢

4. 检查其它慢 sql, 发现如下 sql, 其结束执行时间为 3:28:00, 执行持续时间为 387s, 即其开始执行时间为 3:21:33. 而我们的 sql 执行开始时间为 3:22:14, 正好在其执行之后执行

   ![](http://note.youdao.com/yws/public/resource/340ba176925ee287a13ec8321f3181f3/xmlnote/B35C9BA21AB04241BC04AC2D4164352E/21796)

5. 检查慢 sql 日志中其它天的日志, 情况相同,  因此判断是该 sql 导致我们的 sql 执行缓慢导致 Service 层接口返回超时, 从而导致 Controller 层接口返回空

### 问题

1. 为什么一个慢 SQL 会导致并发的另一个慢 SQL 同时变慢?

   因为磁盘只有一个磁头, 所以磁盘 I/O 是串行的, 两个 SQL 并发查询时. CPU 会在两个线程间来回切换. 从而导致磁头在两个 SQL 所需的扇区中来回切换, 此时磁头是随机读取, 其速度比一个 SQL 查询时的顺序读取慢很多
