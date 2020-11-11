# 链表中常用的技巧

[LeetCode-LinkedList](https://leetcode-cn.com/problemset/all/?topicSlugs=linked-list) 

## 链表基础

 [010707.设计链表](https://github.com/CalmCenter/LeetCode/tree/master/链表_LinkedList/010707_设计链表)  链表的基础初始化和操作

## 快慢指针法

快慢指针中的快慢指的是移动的步长，即每次向前移动速度的快慢。例如可以让快指针每次沿链表向前移动 2，慢指针每次向前移动 1 次。

**使用场景 1：寻找链表中间节点**
这可能是最常用的一种比较方便的方式了，可以查看 [010876. 链表的中间结点](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010876. 链表的中间结点「中间节点」) 学习。

**使用场景 2：链表中是否存在环**
因为快指针是慢指针速度的两倍，所以如果存在环，快指针一定会追上慢指针，这样就可以判断是否存在环 [010141. 环形链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010141. 环形链表「是否有环」)  ，如果要返回入环点，还需要进一步分析 [010142. 环形链表II](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010142. 环形链表II「返回环入口」) 

## 递归

递归在链表中，就是一个倒序遍历（从后往前遍历）。

**使用场景 ：反转**
因为反转是到着遍历，在单链表中，我们可以拿到以及，到着让**下一个节点**指向**当前节点**，就可以达到反转的目的，具体查看 [010206. 反转链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010206. 反转链表「反转」)

**反转进阶：** 
[020006. 从尾到头打印链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/020006. 从尾到头打印链表「反转」) ：反转后保存到数组中
[010024. 两两交换链表中的节点](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010024. 两两交换链表中的节点) ：两两翻转
[010025. K 个一组翻转链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010025. K 个一组翻转链表) ：KK翻转 ~
[010092. 反转链表 II](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010092. 反转链表 II) ：翻转某个区间的节点

## 基础功能：删除节点

在链表中删除某个下标的节点，我们应该考虑哪些问题？删除第一个节点是需要考虑吗？
当然需要考虑第一个节点，我们可以使用**哨兵节点**或者**递归**来解决这一问题，具体请看 [010203. 移除链表元素「删除节点」](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010203. 移除链表元素「删除节点」)  
那如果没给我们链表，也没告诉我们要删除第几个，只告诉我们要删除的节点，单链表中我们也取不到前驱节点，那该怎么删除呢？
其实很简单，单链表中我们只能取到 `next` 的值，所以我们可以将 `next` 的值赋给当前节点，然后将 `next` 节点删除掉，就相当于删除当前节点了啊 ~具体请看 [010237. 删除链表中的节点](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010237. 删除链表中的节点) 

**删除进阶**
[020022. 链表中倒数第 k 个节点](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/020022. 链表中倒数第k个节点) 笨鸟指针（我自己想的 ~），就是 `fast` 指针先走 `k` 步然后与 `slow` 一起前进，他们之前会一直相差 `k` 步，当 `fase` 到达末尾时，`slow` 就是倒数第 `k` 个啊 ~ 
然后可以看这题 [010019. 删除链表的倒数第 N 个节点](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010019. 删除链表的倒数第N个节点「双指针」) 上一题就是为这道题做铺垫，寻到然后进行删除，删除时我们就需要考虑到是 寻找时让 `fast` 在快一个节点，`slow` 就可以拿到倒数第 `k+1` 的节点，可以方便的进行删除，还是拿到第 `k` 的节点使用 `010237` 刚才讲过的删除方式。这两种方式可以

[010082. 删除排序链表中的重复元素 II](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010082. 删除排序链表中的重复元素 II「排序链表」「只保留没有重复的」) 删除**有序链表**中的重复元素，只要有重复的就都删除，保留没有重复的元素。
[010083. 删除排序链表中的重复元素](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010083. 删除排序链表中的重复元素「排序链表」「每个元素出现一次」)  删除**有序链表**中的重复元素，使得每个元素只出现一次
[030201. 移除重复节点](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/030201. 移除重复节点「未排序链表」「每个元素出现一次」) 删除**无序链表**中的重复元素，使得每个元素只出现一次

## 基础功能：合并

我们可以合并两个有序链表，最简单的方式就是循环比大小然后重新组织链表，还可以提高逼格使用递归，具体请看 [010021. 合并两个有序链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010021. 合并两个有序链表「合并」) 
当前也可以合并多个有序链表，还能循环比大小吗？有没有什么更好的方法呢？具体请看：[010023. 合并 K 个升序链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010023. 合并 K 个升序链表「堆」「分治算法」「多个链表合并」) 

## 链表排序

[010147. 对链表进行插入排序](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010147. 对链表进行插入排序「插入排序」) 插入排序
[010148. 排序链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010148. 排序链表「归并排序」「中间节点」「合并」) 归并排序

## 练习

既然学了链表的递归、快慢指针和反转，那就试试这道题吧 ~ [010234. 回文列表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010234. 回文列表「检测回文」) 这道题可以把这些知识到锻炼到 ~ 

如何查看两个链表是否有相交节点？相交节点是哪一个呢？
[010160. 相交链表「两个链表的第一个公共节点」](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010160. 相交链表「两个链表的第一个公共节点」) 这道题使用







[010086. 分割链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010086. 分割链表「基准分割」) 重排链表，用 双指针 或 头插法 ，将链表分为两部分，前一部分是小于某个值的，后一部分是大于等于某个值的。

[010725. 分割链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010725. 分割链表「分组」) 链表拆分到 `k` 个数组中

[010238. 奇偶链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010238. 奇偶链表) 重排链表，前半部分是奇数位节点，后半部分是偶数位节点

[010143. 重排链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010143. 重排链表「反转」「交叉合并」) 重排链表，基础的应用，快慢指针、链表反转、交叉合并， 将前半部分，以及后半部分的反转，交叉合并









[010061. 旋转链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010061. 旋转链表) 形成环后重新确立头节点，顺序不变，只是断开的地方变了。





[010002. 两数相加](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010002. 两数相加「数位反向存放」) 递归 头为个位

[010445. 两数相加 II](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010445. 两数相加 II「链表求和」) 栈 头为最高位





进阶练习

[010430. 扁平化多级双向链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010430. 扁平化多级双向链表) 

[011019. 链表中的下一个更大节点](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/011019. 链表中的下一个更大节点)

[011171. 从链表中删去总和值为零的连续节点](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/011171. 从链表中删去总和值为零的连续节点)

[011367. 二叉树中的列表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/011367. 二叉树中的列表) 二叉树中，是否存在一条路径一一对应链表中的每个节点的值

[010109. 有序链表转换二叉搜索树](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010109. 有序链表转换二叉搜索树「分治」) 快慢指针找到中位节点然后使用分治算法，或者 中序遍历 实现 二叉搜索树

[010138. 复制带随机指针的链表](https://github.com/CalmCenter/LeetCode/tree/master/链表(Linked List)/010138. 复制带随机指针的链表「哈希表」) 递归实现 复制存在随机指针的链表