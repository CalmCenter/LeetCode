# 链表中常用的技巧

题目来自于 [LeetCode-LinkedList](https://leetcode-cn.com/problemset/all/?topicSlugs=linked-list) 

## 链表基础

 [010707.设计链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010707_设计链表)  `基础` 链表的基础初始化和操作

双指针练习 [011721_交换链表中的节点](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/011721_交换链表中的节点)

## 快慢指针法

快慢指针中的快慢指的是移动的步长，即每次向前移动速度的快慢。例如可以让快指针每次沿链表向前移动 2，慢指针每次向前移动 1 次。

**使用场景 1：寻找链表中间节点**

这可能是最常用的一种比较方便的方式了，可以查看 [010876_链表的中间结点](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010876_链表的中间结点) `基础` 学习。

**使用场景 2：链表中是否存在环**

因为快指针是慢指针速度的两倍，所以如果存在环，快指针一定会追上慢指针，这样就可以判断是否存在环 [010141_环形链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010141_环形链表) `基础、面试题`，如果要返回入环点，还需要进一步利用数学知识分析节点长度的关系 [010142_环形链表II](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010142_环形链表II) `面试题`



## 递归

递归在链表中，就是一个倒序遍历（从后往前遍历）。

**使用场景 ：反转**

因为反转是到着遍历，在单链表中，我们可以拿到以及，到着让**下一个节点**指向**当前节点**，就可以达到反转的目的，具体查看 [010206_反转链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010206_反转链表) `基础` 

**反转进阶：** 

- [020006_从尾到头打印链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/020006_从尾到头打印链表) ： `剑指 Offer` 反转后保存到数组中
- [010024_两两交换链表中的节点](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010024_两两交换链表中的节点) ：两两翻转
- [010025_K 个一组翻转链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010025_K个一组翻转链表) ：KK翻转 ~
- [010092_反转链表II](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010092_反转链表II) ：翻转某个区间的节点

## 基础功能：删除节点

在链表中删除某个下标的节点，我们应该考虑哪些问题？删除第一个节点是需要考虑吗？

当然需要考虑第一个节点，我们可以使用**哨兵节点**或者**递归**来解决这一问题，具体请看 [010203_移除链表元素](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010203_移除链表元素) `基础` 

那如果没给我们链表，也没告诉我们要删除第几个，只告诉我们要删除的节点，单链表中我们也取不到前驱节点，那该怎么删除呢？

其实很简单，单链表中我们只能取到 `next` 的值，所以我们可以将 `next` 的值赋给当前节点，然后将 `next` 节点删除掉，就相当于删除当前节点了啊 ~具体请看 [010237_删除链表中的节点](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010237_删除链表中的节点) `基础、面试题`

**删除进阶**

[020022_链表中倒数第k个节点](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/020022_链表中倒数第k个节点) `面试题` 笨鸟指针（我自己想的 ~），就是 `fast` 指针先走 `k` 步然后与 `slow` 一起前进，他们之前会一直相差 `k` 步，当 `fase` 到达末尾时，`slow` 就是倒数第 `k` 个啊 ~ 

然后可以看这题 [010019_删除链表的倒数第N个节点](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010019_删除链表的倒数第N个节点)  上一题就是为这道题做铺垫，寻到然后进行删除，删除时我们就需要考虑到是 寻找时让 `fast` 在快一个节点，`slow` 就可以拿到倒数第 `k+1` 的节点，可以方便的进行删除，还是拿到第 `k` 的节点使用 `010237` 刚才讲过的删除方式。这两种方式可以

- [010082_删除排序链表中的重复元素II](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010082_删除排序链表中的重复元素II) 删除**有序链表**中的重复元素，只要有重复的就都删除，保留没有重复的元素。
- [010083_删除排序链表中的重复元素](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010083_删除排序链表中的重复元素) 删除**有序链表**中的重复元素，使得每个元素只出现一次
- [030201_移除重复节点](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/030201_移除重复节点) `面试题` 删除**无序链表**中的重复元素，使得每个元素只出现一次

## 基础功能：合并

我们可以合并两个有序链表，最简单的方式就是循环比大小然后重新组织链表，还可以提高逼格使用递归，具体请看 [010021_合并两个有序链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010021_合并两个有序链表) `基础` 
当前也可以合并多个有序链表，还能循环比大小吗？有没有什么更好的方法呢？具体请看：[010023_合并K个升序链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010023_合并K个升序链表) `进阶` 

## 链表排序

[010147_对链表进行插入排序](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010147_对链表进行插入排序) 插入排序

[010148_排序链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010148_排序链表) 归并排序

## 链表算术

[010002_两数相加](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010002_两数相加) `面试题` 链表头为个位，循环两个链表每一位进行相交，并且需要考虑到进位

[010445_两数相加II](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010445_两数相加II) `面试题` 链表头为最高位，需要从**两个链表末尾**开始遍历，想到倒着遍历第一时间是想到的递归，但是递归无法在两个长短不一的链表中同时倒着遍历，所以可以使用栈。

## 练习

既然学了链表的递归、快慢指针和反转，那就试试这道题吧 ~ [010234_回文列表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010234_回文列表) `面试题` 这道题可以把这些知识到锻炼到 ~ 

如何查看两个链表是否有相交节点？相交节点是哪一个呢？

[010160_相交链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010160_相交链表) `面试题` 这道题数学知识用到了链表上，虽然是很简单的一道加法题，但是就是想不到这么个解法 ~ 

**链表的拆分与重排练习**

- [010086_分割链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010086_分割链表) `面试题` 重排链表，用 双指针 或 头插法 ，将链表分为两部分，前一部分是小于某个值的，后一部分是大于等于某个值的。
- [010725_分割链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010725_分割链表) `进阶` 链表拆分到 `k` 个数组中
- [010238_奇偶链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010238_奇偶链表) `进阶` 重排链表，前半部分是奇数位节点，后半部分是偶数位节点
- [010143_重排链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010143_重排链表) `进阶` 重排链表，基础的应用，快慢指针、链表反转、交叉合并， 将前半部分，以及后半部分的反转，交叉合并

圈圈圆圆圈圈 ~ [010061_旋转链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010061_旋转链表) 这道题想到与找到倒数第 `k` 个节点，作为新的头，将链表原来的尾和原来的头相连，这是一种思路，题中的解法是形成环后再找第 `n - k % n` 的节点作为新的头点。

## 进阶练习

[010430_扁平化多级双向链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010430_扁平化多级双向链表) 

[011019_链表中的下一个更大节点](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/011019_链表中的下一个更大节点) 

[011171_从链表中删去总和值为零的连续节点](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/011171_从链表中删去总和值为零的连续节点) 

[011367_二叉树中的列表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/011367_二叉树中的列表) 二叉树中，是否存在一条路径一一对应链表中的每个节点的值

[010109_有序链表转换二叉搜索树](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010109_有序链表转换二叉搜索树) 快慢指针找到中位节点然后使用分治算法，或者 中序遍历 实现 二叉搜索树

[010138_复制带随机指针的链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010138_复制带随机指针的链表) `剑指 Offer` 递归实现 复制存在随机指针的链表