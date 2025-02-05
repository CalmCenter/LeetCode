给你一个正整数数组 `arr`，考虑所有满足以下条件的二叉树：

- 每个节点都有 0 个或是 2 个子节点。
- 数组 `arr` 中的值与树的中序遍历中每个叶节点的值一一对应。（知识回顾：如果一个节点有 0 个子节点，那么该节点为叶节点。）
- 每个非叶节点的值等于其左子树和右子树中叶节点的最大值的乘积。

在所有这样的二叉树中，返回每个非叶节点的值的最小可能总和。这个和的值是一个 32 位整数。

 **示例：**

```
输入：arr = [6,2,4]
输出：32
解释：
有两种可能的树，第一种的非叶节点的总和为 36，第二种非叶节点的总和为 32。

    24            24
   /  \          /  \
  12   4        6    8
 /  \               / \
6    2             2   4

```

**提示：**

- `2 <= arr.length <= 40`
- `1 <= arr[i] <= 15`
- 答案保证是一个 32 位带符号整数，即小于 `2^31`。

# 题解

维护一个单调递减栈，单调递减栈有一个特性。

如果我们遇到一个比栈顶元素大元素，于是乎就有了两种情况。

我们就看前面 3 个。

第一种情况：大 小 中，这样只会将小出栈。

- 大小放一块：`大 * 小 + 大 * 中`
- 小中放一块：`小 * 中 + 大 * 中`
- 所以把小和中放在一起得到的值更小。

第二种情况： 中 小 大， 小 中 都会出栈

- 中小放在一块：`中 * 小 + 中 * 大`
- 小大放在一块：`小 * 大 + 中 * 大`
- 因此把中小放在一起得到的值更小。

由此得出结论如果遇到出栈情况，我们将小的那个(也就是栈顶元素)和它左边或右边较小的那个放在一起得到的答案是最优。

```java
  24
 /  \
6    12
    /  \
   6    4
  / \
 2   3
      
      24 
     /  \
    12   6
   /  \
  6    4
 / \
3   2
```

为什么较小的叶子节点就要尽量放到底部，值较大的叶子节点要尽量放到靠上的部分？

因为非叶节点都是左右叶子节点中最大的那个的乘积。放在底部那两个里面大的那个必然会被每次都使用，越靠下使用的次数越多，肯定要选最小的值往下放，才能让 `mct` 尽量小。