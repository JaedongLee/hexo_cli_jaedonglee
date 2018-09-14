---
title: JEECMS获取指定栏目标题图
date: 2017-10-24 15:06:57
tags:
category: JEECMS
---
- 获取指定栏目标题图的方法:     
&emsp;&emsp;` [@cms_channel id='175']  
                <img src="${tag_bean.titleImg!site.defImg}" alt="${tag_bean.name}" width="100%" />  
            [/@cms_channel]`
    - 注意: alt中不能为title, 因为没有这个字段, 这个字段是属于content的
- 获取批量栏目标题图的方法:
    - ` [@cms_channel_list ] <!-- more -->
        [#list tag_list as a] 
            <img src="${a.titleImg!site.defImg}" alt="${a.name}" width="100%" /> 
        [/#list] 
        [/@cms_channel_list]`
    - 注意: 如果a.name不行请使用, 请使用a.title尝试hexo