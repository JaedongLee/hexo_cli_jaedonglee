---
title: Programming-Note
date: 2018-01-04 18:27:33
tags:
category: Programming
---
- Skill
    - 跳过循环的下一次
        - 可以在循环外设置一个变量保存值, 当需要跳过下一次循环时, 改变该值, 再判断变量的值, 如是指定值,则结束本次循环, 这样就跳过了一次循环
        - code:<!-- more -->
            ```
            def merge(row):
                pair = False
                new_row = []
                for i in range(len(row)):

                    if i + 1 < len(row) and row[i] == row[i + 1]:
                        pair = True
                        new_row.append(0)
                        new_row.append(2 * row[i])
                        i = i + 1
                    else:
                        new_row.append(row[i])
                assert len(new_row) == len(row)
                return new_row
            ```