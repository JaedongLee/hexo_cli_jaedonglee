---
title: Java-Arithmetic-Note
date: 2018-03-16 17:28:57
tags: Arithmetic
category: Java
---
1.  - question: `Jewels And Stones`
    ```
    You're given strings J representing the types of stones that are jewels, and S representing the stones you have.  Each character in S is a type of stone you have.  You want to know how many of the stones you have are also jewels.

    The letters in J are guaranteed distinct, and all characters in J and S are letters. Letters are case sensitive, so "a" is considered a different type of stone from "A".
<!-- more -->
    Example 1:

    Input: J = "aA", S = "aAAbbbb"
    Output: 3
    Example 2:

    Input: J = "z", S = "ZZ"
    Output: 0
    Note:

    S and J will consist of letters and have length at most 50.
    The characters in J are distinct.
    ```
    - answer: 
    ```
    package jewelsAndStones;

    import java.util.*;

    /**
    * @Auther: 1nfinity
    * @Description: 先把 stone 中的字符及其个数统计出来并放入 Map 中, 然后在其中寻找对应的 jewel 的数量. 这样应该比直接在 stones 中检索要快
    * @Date: Created in 02:53 PM 3/16/2018
    * @Modified By:
    */
    public class Solution {
        public static int numJewelsInStones(String J, String S) {
            // 获取 Stones 字符串的字符列表
            // 仅适用 string.toCharArray 获取到的只是数组, 无法对其进行 remove 操作, 因此需要循环成 List
            List<Character> chars = new ArrayList();
            for (char c : S.toCharArray()) {
                chars.add(c);
            }
            Map<Character, Integer> charsAndNumMap = new HashMap();
            // 将 List 的第一个字符和其它字符相比较, 如果相同, 数量+1并删除该字符
            // 将上述过程循环进行, 直到 List 的 size 为零, 得到包含 stone 类型和数量的 Map
            // 由于 chars.size() 一直在变动, 因此不适合以其为边界使用 for().
            // for() 并非必须使用, 尽量在有固定边界条件时使用
            while (chars.size() > 0) {
                int count = 1;
                for (int i = 1; i < chars.size();) {
                    if (chars.get(0) == chars.get(i)) {
                        chars.remove(i);
                        count++;
                    }else {
                        // 只有在第一个字符未匹配到相同字符时才进行 i++, 因为 i++ 不是必须的, 只是为了推动循环向前进行, 当匹配时, 已经对匹配到的字符进行了移除, 减小了 size, 相当于已经进行了 i++, 已经推进了循环.
                        i++;
                    }
                }
                charsAndNumMap.put(chars.get(0), count);
                chars.remove(0);
            }
            // 遍历 jewel, 尝试在 Map 中获取其数量, 将所有类型的 jewel 的数量相加
            char[] jewels = J.toCharArray();
            int jewelNum = 0;
            for (char jewel : jewels) {
                if (charsAndNumMap.containsKey(jewel)) {
                    jewelNum += charsAndNumMap.get(jewel);
                }
            }
            System.out.println("stone type: " + charsAndNumMap.keySet());
            System.out.println("stone num: " + charsAndNumMap.values());
            System.out.println("jewels total num: " + jewelNum);
            return jewelNum;
        }
        public static void main(String[] args) {
            numJewelsInStones("ngm", "kxga");
        }
    }

    ```
2.  - question: `Judge Route Circle`
    ```
    Initially, there is a Robot at position (0, 0). Given a sequence of its moves, judge if this robot makes a circle, which means it moves back to the original place.

    The move sequ`ence is represented by a string. And each move is represent by a character. The valid robot moves are R (Right), L (Left), U (Up) and D (down). The output should be true or false representing whether the robot makes a circle.

    Example 1:
    Input: "UD"
    Output: true
    Example 2:
    Input: "LL"
    Output: false`
    ```
    - answer:
        - myAnswer: 
        ```
        public class Solution {
            public boolean judgeCircle(String moves) {
                int x = 0;
                int y = 0;
                for (char c : moves.toCharArray()) {
                    switch (c) {
                        case 'L':
                            x--;
                            break;
                        case 'R':
                            x++;
                            break;
                        case 'U':
                            y++;
                            break;
                        case 'D':
                            y--;
                            break;
                    }
                }
                return x + y == 0;
            }
        }
        ```
        - bestAnswer:
        ```
        class Solution {
            public boolean judgeCircle(String moves) {
                // use an int array to save letter count, its index is the ascii num of the letter
                int temp[] = new int[128];
                for (char c : moves.toCharArray()) {
                    temp[c]++;
                }
                return temp['U'] == temp['D'] && temp['L'] == temp['R'];
            }
        }
        ```
3.  - question: `Self Dividing Numbers`
    ```
    A self-dividing number is a number that is divisible by every digit it contains.

    For example, 128 is a self-dividing number because 128 % 1 == 0, 128 % 2 == 0, and 128 % 8 == 0.

    Also, a self-dividing number is not allowed to contain the digit zero.

    Given a lower and upper number bound, output a list of every possible self dividing number, including the bounds if possible.

    Example 1:
    Input: 
    left = 1, right = 22
    Output: [1, 2, 3, 4, 5, 6, 7, 8, 9, 11, 12, 15, 22]
    Note:

    The boundaries of each input argument are 1 <= left <= right <= 10000.
    ```
    - answer:
        - myAnswer:
        ```
        package selfDividingNumbers;

        import java.util.ArrayList;
        import java.util.List;

        /**
        * @Auther: 1nfinity
        * @Description:
        * @Date: Created in 09:46 AM 3/20/2018
        * @Modified By:
        */
        public class Solution {
            public List<Integer> selfDividingNumbers(int left, int right) {
                List<Integer> integers = new ArrayList<>();
                while (left <= right) {
                    if (judgeRange(left, left, 10000)) {
                        integers.add(left);
                    }
                    left++;
                }
                return integers;
            }

            // 整体逻辑: 从最大的位数开始进行模运算
            // source: 需要判断的数字; reminder: 用来记录删去最高位后的数字; divide: 除数, 开始为10000;
            private boolean judgeRange(int source, int reminder, int divide) {
                // 必要, 不然 reminder / divide 会报错
                if (divide < 1) {
                    return true;
                }
                // 如果为真, 则表明无法通过 source / divide 来获取source的最高位, 因此需要减少 divide
                if (source < divide) {
                    return judgeRange(source, reminder,divide / 10);
                }
                // 表明 reminder 的最高位为 0, 需要排除
                if (reminder / divide == 0) {
                    return false;
                }
                // Note! 当 source 模 最高位 为 0 时, 进行递归, 直到模所有位都为 0 返回 true, 或者有不为 0, 返回 false
                if (source % (reminder / divide) == 0) {
                    return judgeRange(source, reminder % divide, divide / 10);
                }
                return false;
            }

            public static void main(String[] args) {
                Solution solution = new Solution();
                System.out.println(solution.selfDividingNumbers(1, 213));
            }

        }
        ```
        - bestAnswer:
        ```
        public class BetterSolution {
            public List<Integer> selfDividingNumbers(int left, int right) {
                List<Integer> ans = new ArrayList();
                for (int n = left; n <= right; ++n) {
                    if (selfDividing(n)) ans.add(n);
                }
                return ans;
            }
        //    Alternate implementation of selfDividing:
        //    // 通过将源正整数转换为字符串来获取其各个位数
        //    public boolean selfDividing(int n) {
        //        for (char c: String.valueOf(n).toCharArray()) {
        //            if (c == '0' || (n % (c - '0') > 0))
        //                return false;
        //        }
        //        return true;
        //    }
            // 整体逻辑: 从最低位开始进行模运算, 使用的定理: 任何正整数与 10 模, 获得这个正整数的最低位; 任何正整数与 10 除, 获得其除去最低位的数字. 模和除本质上会互补关系
            public boolean selfDividing(int n) {
                int x = n;
                while (x > 0) {
                    int d = x % 10;
                    // 减少 x 的值, 用来依次获取最低位
                    x /= 10;
                    // 如果最低位为 0, 或者源正整数模最低位 不为 0, 排除该数
                    if (d == 0 || (n % d) > 0) return false;
                }
                return true;
            }
        }
        ```