# 基础知识

关于 “栈”，就像是一摞叠在一起的盘子。我们平时放盘子的时候，都是从下往上一个一个叠放在一起；取的时候，我们也是从上往下一个一个地依次取，不能从中间任意抽出。**后进者先出，先进者后出，这就是典型的 “栈” 结构**。

从栈的操作特性上来看，栈是一种 “操作受限” 的线性表，只允许在一端插入和删除数据。相比数组和链表，栈带给我们的只有限制，并没有任何优势。那直接使用数组或者链表不就好了吗？为什么还要用这个 “操作受限” 的 “栈” 呢？

> 事实上，从功能上来说，数组或链表确实可以替代栈，但要知道，特定的数据结构是对特定场景的抽象，而且，数组或链表暴露了太多的操作接口，操作上的确灵活自由，但使用时就比较不可控，自然也就更容易出错。

所以，当某个数据集合只涉及在一端插入和删除数据，并且满足后进先出、先进后出的特性，这时我们就应该首选 “栈” 这种数据结构。

## 实现一个 “栈”

栈既可以用数组来实现，也可以用链表来实现。用数组实现的栈，我们叫作**顺序栈**，用链表实现的栈，我们叫作**链式栈**。

```java
// 基于数组实现的顺序栈
public class ArrayStack {
  private String[] items;  // 数组
  private int count;       // 栈中元素个数
  private int n;           //栈的大小

  // 初始化数组，申请一个大小为n的数组空间
  public ArrayStack(int n) {
    this.items = new String[n];
    this.n = n;
    this.count = 0;
  }

  // 入栈操作
  public boolean push(String item) {
    // 数组空间不够了，直接返回false，入栈失败。
    if (count == n) return false;
    // 将item放到下标为count的位置，并且count加一
    items[count] = item;
    ++count;
    return true;
  }
  
  // 出栈操作
  public String pop() {
    // 栈为空，则直接返回null
    if (count == 0) return null;
    // 返回下标为count-1的数组元素，并且栈中元素个数count减一
    String tmp = items[count-1];
    --count;
    return tmp;
  }
}
```

它的空间复杂度，不管是顺序栈还是链式栈，存储数据只需要一个大小为 `n` 的数组，在入栈和出栈过程中，只需要一两个临时变量存储空间，所以空间复杂度是 `O(1)` 。

> **注意**，这里存储数据需要一个大小为 `n` 的数组，并不是说空间复杂度就是 `O(n)`。因为，这 `n` 个空间是必须的，无法省掉。所以我们说空间复杂度的时候，**是指除了原本的数据存储空间外，算法运行还需要额外的存储空间。**

它的时间复杂度也不难。不管是顺序栈还是链式栈，入栈、出栈只涉及栈顶个别数据的操作，所以时间复杂度都是 `O(1)`。

> 如果是支持动态扩容的顺序栈，对于入栈操作，当栈中有空闲空间时，入栈操作的时间复杂度为 `O(1)`。但当空间不够时，就需要重新申请内存和数据搬移，所以时间复杂度就变成了 `O(n)`。
>
> 也就是说，对于入栈操作来说，最好情况时间复杂度是 `O(1)`，最坏情况时间复杂度是 `O(n)`。
>
> 如果当前栈大小为 `K`，并且已满，当再有新的数据要入栈时，就需要重新申请 `2` 倍大小的内存，并且做 `K` 个数据的搬移操作，然后再入栈。但是，接下来的 `K-1` 次入栈操作，我们都不需要再重新申请内存和搬移数据，所以这 `K-1` 次入栈操作都只需要一个 `simple-push O(1) ` 操作就可以完成。
>
> 可以看出来，这 `K` 次入栈操作，总共涉及了 `K` 个数据的搬移，以及 `K` 次 `simple-push` 操作。将 `K` 个数据搬移均摊到 `K` 次入栈操作，那每个入栈操作只需要一个数据搬移和一个 `simple-push` 操作。以此类推，入栈操作的均摊时间复杂度就为 `O(1)`。
>
> 均摊时间复杂度一般都等于最好情况时间复杂度。因为在大部分情况下，入栈操作的时间复杂度 `O` 都是 `O(1)`，只有在个别时刻才会退化为 `O(n)`，所以把耗时多的入栈操作的时间均摊到其他入栈操作上，平均情况下的耗时就接近 `O(1)`。

# 题目整理

做题之前看到了这篇文章 [Java 程序员，别用 Stack？！](https://mp.weixin.qq.com/s/Ba8jrULf8NJbENK6WGrVWg) ，这里简单说明一下

为什么不推荐使用 `Stack` 类？

官方解释到：一个更加完整，一致的，后进先出的栈相关的操作，应该由 `Deque` 接口提供。并且，也推荐使用 `Deque` 这种数据结构（比如 `ArrayDeque`）来实现。

```java
Deque<Integer> stack = new ArrayDeque<>();
```

而 `Java` 中的 `Stack` 中，问题就在于它继承了 `Vector` 这个类。

>  `Vector` 是什么类？`Vector` 就是一个动态数组。动态数组，是有能力在数组中的**任何位置**添加或者删除元素的。

继承使得子类继承了父类的所有公有方法，因此，`Stack` 也可以在任何位置添加或者删除元素。这显然破坏了栈这种结构的封装。更多说明请参考上面链接。

## 基础题目

[010020_有效括号](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010020_有效括号)  `基础` 左右括号 匹配

[010071_简化路径](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010071_简化路径)  `基础`  通过栈操作路径的前进和后退，输出最终路径

[010155_最小栈](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010155_最小栈)  `基础` `进阶思维` 记录栈中最小值

[010394_字符串解码](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010394_字符串解码) `基础` 



## 计算

[010150_逆波兰表达式求值](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010150_逆波兰表达式求值) `基础` 通过栈计算 [ 逆波兰表示法](https://baike.baidu.com/item/逆波兰式/128437) 得出的结果

[010224_基本计算器](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010224_基本计算器) `基础` 使用栈完成数字的加减法

[010227_基本计算器II](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010227_基本计算器II) `基础` 栈的基本使用，思路上先计算乘除，加减只做保存，最后统一加在一起



## 队列与栈

两个队列或两个栈相互合作的思路。

[010225_用队列实现栈](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010225_用队列实现栈) 加深对栈数据结构与队列数据结构的理解

[010232_用栈实现队列](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010232_用栈实现队列)



## 单调栈

单调栈就是栈里面存放的数据都是有序的，所以可以分为单调递增栈和单调递减栈两种。

[010084_柱状图中最大的矩形](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010084_柱状图中最大的矩形)  `单调递增栈`  栈只添加比当前栈顶大的，碰到小值后出栈计算最大矩形。

[010085_最大矩形](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010085_最大矩形)  `单调递增栈` 解法同 `84` 题思路。

[010042_接雨水](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010042_接雨水) `困难` `动态规划`  `双指针`   `单调递减栈` 栈只添加比当前栈顶小的，碰到大值后，证明形成了洼地，计算可以接多少雨水。



## 贪心算法

贪心算法（又称贪婪算法）是指，在对问题求解时，总是做出在当前看来是最好的选择。也就是说，不从整体最优上加以考虑，算法得到的是在某种意义上的**局部最优解。**

贪心算法不是对所有问题都能得到整体最优解，关键是贪心策略的选择。

[010316_去除重复字母](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010316_去除重复字母) `贪心` `局部单调递增栈` 

[010402_移掉K位数字](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010402_移掉K位数字)   `贪心`  `单调递增栈`



## 二叉树

涉及到栈的遍历，递归以及`Morris` 

[010094_二叉树的中序遍历](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010094_二叉树的中序遍历)  

[010144_二叉树的前序遍历](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010144_二叉树的前序遍历) 

[010145_二叉树的后序遍历](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010145_二叉树的后序遍历)

[010173_二叉搜索树迭代器](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010173_二叉搜索树迭代器)

[010331_验证二叉树的前序序列化](https://github.com/CalmCenter/LeetCode/tree/master/栈_Stack/010331_验证二叉树的前序序列化) `思路`

















