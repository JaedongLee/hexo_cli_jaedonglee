---
title: JEECMS栏目自定义属性研究
date: 2017-10-24 10:49:54
tags:
category: JEECMS
---
可以为栏目添加自定义属性
- 实现步骤: 
    1. 在配置中的模型管理中为所需的模型添加所需的字段名
        - 此时会在数据库的jc_channel_attr表中的channel_id 和 attr_name分别产生一个记录
    2. 在栏目中点击修改, 找到新添加的字段, 输入想要的值
        - 此时会在数据库的jc_channel_attr表中的attr_value中产生一个记录
    3. 在栏目模板对应的html文件(如: C:\Program Files (x86)\Apache Software Foundation\Tomcat <!-- more -->8.5\webapps\ROOT\WEB-INF\t\cms\www\default\channel\create_cast_agencies.html)中使用channel.attr.custom_value来显示为栏目所设置的属性