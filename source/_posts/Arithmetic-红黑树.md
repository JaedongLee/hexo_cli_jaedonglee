---
title: Arithmetic-红黑树
date: 2018-04-01 11:43:38
tags:
category: Arithmetic
---
- 红黑树结构剖析
    - 参考文档: [`红黑树数据结构剖析`](http://www.cnblogs.com/fanzhidongyzby/p/3187912.html); `算法导论`
    - 基本概念
        - 规则
            1. 节点非红即黑<!-- more -->
            1. 根节点必须是黑色的
            2. 叶子节点是黑色的
            2. 任意从根到叶子节点的路径不包含连续的红色节点
            3. 任意从根到叶子节点的路径的黑色节点总数相同
    - 数据结构设计
        - 作为红黑树节点, 其基本属性有: 节点的颜色/左子节点指针/右子节点指针/父节点指针/节点值
    - 红黑树的插入操作
        - 如果插入第一个节点, 我们直接用根节点记录它, 并设置为黑色
        - 如果不是, 则默认插入的节点颜色为红色, 因为插入黑色的节点会破坏根路径上的节点总数
        - 插入后调整: 在插入后不满足上述5条规则时进行调整
            - 原因: 插入节点和父节点的颜色都是红色, 发生冲突        
            ![插入冲突](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/C16EE8D56EFA4CF0924E7A513C4DED25/17128)
        - 插入问题分类
            1. 叔父节点为黑色
                - 解决思路: 将多出的节点移到叔父节点那一方
                - 分类
                    - 外侧插入
                        1. n是p的左子节点, p是g的左子节点       
                        ![情况1](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/BA6CEBFC7D4A4366B8F2FB5062FCFFE0/17127)
                        2. n是p的右子节点, p是g的右子节点       
                        ![情况2](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/206509A8D34F4BB3A7A4EFBAA35949CB/17125)
                    - 内侧插入
                        1. n是p的左子节点, p是g的右子节点       
                        ![情况3](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/586018389AAA4455BFD3A944434B8EF5/17123)
                        2. n是p的右子节点, p是g的左子节点       
                        ![情况4](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/C7D9B76DCB2148F4BE33A382B021A693/17124)
            2. 叔父节点是红色       
                ![叔父节点为红](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/4DB3631B84C94FB9979DBA854946DDD9/17130)
                - 解决思路: 将p/u节点变成黑色, 此时路径上黑色数量增加1, 将g变为红色, 再通过操作解决g和gp的冲突
                - 操作: 从当前插入点n向根节点root回溯两次
                    1. 第一次回溯处理`所有`拥有两个红色节点的节点, 并按照上图的方式进行处理, 同时暂时忽略gp和p的颜色冲突。如果根节点的两个子节点也是这种情况，则在颜色交换完毕后重新将根节点设置为黑色。
                    2. 第二次回溯专门处理连续的红色节点冲突。由于经过第一遍的处理，在新插入点n的路径上一定不存在同为红色的兄弟节点了。而仍出现gp和p的红色冲突时，gp的兄弟节点（gu）可以断定为黑色，这样就`回归前边讨论的叔父节点为黑色时的情况处理`。     
                    ![消除连续红色节点](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/698412B9E7BF45A8810BFD6ABFC7809A/17129)
    - 红黑树的删除操作
        1. 如只有唯一子节点或没有子节点, 则该节点删除, 并将其子节点代替自身位置
        2. 如有两个子节点, 则从右子树中选择最小值节点y(此节点无左节点, 因为其最小), 将y的值拷贝到待删除节点yold(yold颜色不变), 从而将问题转化成独立后继的问题(即只有一个右子节点)         
        ![删除点的选定](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/1425446FFECD40BCBE43EBE764535C68/17119)
            - 考虑被删除节点y的颜色
                1. 为红, 删除后不影响平衡性
                2. 为黑, 该路径上的黑节点少1, 需进行调整
                    - 考虑n的颜色
                        1. 为红, 将n变为黑, 则恢复平衡性
                        2. 为黑, 按下面步骤考虑(设p为n的父节点, w为n的兄弟节点)
                            - 解决思路: 从兄弟节点借调黑色节点过来, 兄弟节点将红色节点变成黑色以保持平衡性
                            1. w为红色, w的父/子节点必为黑色, 此时进行如下操作: 将w和p交换颜色, 并将w左旋, 这样讲问题转换成了`步骤2`能解决的问题        
                            ![节点删除情况1](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/C00ABBEE00DA43DF9AD9D589DA2C8DC7/17120)
                            2. w为黑色, 本步骤包括但不限于步骤1, 因为p可能为红. 此时考虑w的子节点:
                                1. w的子节点x/y全为黑色, 此时将w的颜色设置为红, 并将p设置为新的n节点, 重新开始进入`考虑n的颜色`的步骤       
                                ![节点删除情况2](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/425081E4F43646B3BF202A385496CC3E/17122)
                                2. w的子节点x/y为红/黑, 此时将x设置为黑色, w设置为红色, 并以x为轴右旋, 最后将n的新兄弟设置为w, 这样就把问题属于下一种情况: w的右子节点为红, 左节点为黑       
                                ![节点删除情况3](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/932907F1ED214230ABC978595C3AAF33/17121)
                                3. w的右子节点为红, 包含上一种情况. 这时将w的右子节点y设置为黑色，并交换w与父节点p的颜色（w原为黑色，p颜色可黑可红），再以w为旋转轴左旋转，红黑树调整算法结束。         
                                ![节点删除情况4](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/381CF9C0DF0D4A7B8573DB1DCF475121/17126)
        - 删除总结:     
        ![删除流程图](https://note.youdao.com/yws/public/resource/53910c73e7a79185ad0dc5687ae27920/xmlnote/A306213335E0493BBB8EE59A3D064E40/17118)
