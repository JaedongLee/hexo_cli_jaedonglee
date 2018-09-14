---
title: Java-Note-警惕自增陷阱
date: 2018-03-24 08:21:58
tags:
category: Java
---
- 警惕自增的陷阱
    - background: 自增有两种形式: `i++`和`++i`, `i++`表示先赋值后加1, `++i`表示先加1后赋值
    - question: 预测下面代码的结果是什么:<!-- more -->
    ```
    public class Client {
        public static void main(String[] args) {
            int count = 0;
            for (int i = 0; i < 10; i++) {
                count = count++;
            }
            System.out.println("count=" + count);
        }
    }
    ```
    - answer: 答案是`0`
        - reason: 
            - Java对自加的处理是这样的: 首先把`count`的值(注意是值, 不是引用)拷贝到一个临时变量区, 然后对`count`变量加`1`, 然后返回临时变量区的值
            - 而在本例中, 返回的临时变量的值又被赋值给`count`, 此时`count`的值又变成了`0`, 继续循环`count`依旧是`0`
            - 从字节码中分析:
            ```
                public void test1() {
                    int i = 1;
                    i++;
                }

                public void test2() {
                    int i = 0;
                    i = i++;
                }
            Code:
            test1:
            0 iconst_1  常数 0 入栈
            1 istore_1  将栈顶的 0 赋给 变量 i
            2 iinc 1 by 1   变量 i 增加, 栈内值不变
            5 return
            test2:
            0 iconst_0  常数 0 入栈
            1 istore_1  将栈顶的 0 赋给 变量 i
            2 iload_1   装载变量 i, 将 0 压入栈中
            3 iinc 1 by 1   变量 i 增加, 栈内值不变
            6 istore_1  将栈顶的 0 赋给 变量 i(左侧的那个 i)
            7 return
            }
            ```