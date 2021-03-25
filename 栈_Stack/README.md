# 基础知识

关于 “栈”，就像是一摞叠在一起的盘子。我们平时放盘子的时候，都是从下往上一个一个叠放在一起；取的时候，我们也是从上往下一个一个地依次取，不能从中间任意抽出。**后进者先出，先进者后出，这就是典型的 “栈” 结构**。







# 题目整理

做题之前看到了这篇文章 [Java 程序员，别用 Stack？！](https://mp.weixin.qq.com/s/Ba8jrULf8NJbENK6WGrVWg) ，这里简单说明一下

为什么不推荐使用 `Stack` 类？

官方解释到：一个更加完整，一致的，后进先出的栈相关的操作，应该由 `Deque` 接口提供。并且，也推荐使用 `Deque` 这种数据结构（比如 `ArrayDeque`）来实现。

```java
Deque<Integer> stack = new ArrayDeque<>();
```

而 `Java` 中的 `Stack` 中，问题就在于它继承了 `Vector` 这个类。

>  `Vector` 是什么类？`Vector` 就是一个动态数组。动态数组，是有能力在数组中的任何位置添加或者删除元素的。

继承使得子类继承了父类的所有公有方法，因此，`Stack` 也可以在任何位置添加或者删除元素。这显然破坏了栈这种结构的封装。更多说明请参考上面链接。

# 基础题目

[010020_有效括号](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010020_有效括号)  `基础` 左右括号 匹配

[010071_简化路径](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010071_简化路径)  `基础`  通过栈操作路径的前进和后退，输出最终路径

[010150_逆波兰表达式求值](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010150_逆波兰表达式求值) `基础` 通过栈计算 [ 逆波兰表示法](https://baike.baidu.com/item/逆波兰式/128437) 得出的结果

[010155_最小栈](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010155_最小栈)  `基础` `进阶思维` 记录栈中最小值

[010224_基本计算器](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010224_基本计算器) `基础` 使用栈完成数字的加减法

[010227_基本计算器 II](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010227_基本计算器 II) `基础` 栈的基本使用，思路上先计算乘除，加减只做保存，最后同一加在一起

## 队列与栈

两个队列或两个栈相互合作的思路。

[010225_用队列实现栈](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010225_用队列实现栈) 加深对栈数据结构与队列数据结构的理解

[010232_用栈实现队列](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010232_用栈实现队列)

## 单调栈

单调栈就是栈里面存放的数据都是有序的，所以可以分为单调递增栈和单调递减栈两种。

[010084_柱状图中最大的矩形](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010084_柱状图中最大的矩形)  `单调递增栈`  栈只添加比当前栈顶大的，碰到小值后出栈计算最大矩形。

[010085_最大矩形](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010085_最大矩形)  `单调递增栈` 解法同 `84` 题思路。

[010042_接雨水](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010042_接雨水) `困难` `动态规划`  `双指针`   `单调递减栈` 栈只添加比当前栈顶小的，碰到大值后，证明形成了洼地，计算可以接多少雨水。







## 二叉树遍历

涉及到栈的遍历，递归以及`Morris` 

[010094_二叉树的中序遍历](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010094_二叉树的中序遍历)  

[010144_二叉树的前序遍历](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010144_二叉树的前序遍历) 

[010145_二叉树的后序遍历](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010145_二叉树的后序遍历)

[010173_ 二叉搜索树迭代器](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010173_ 二叉搜索树迭代器)



















