## 题目

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 `0` 之外，这两个数都不会以 `0` 开头。

**示例:**

```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

## 解题

### 方法：初等数学



![Add Two Numbers](https://pic.leetcode-cn.com/Figures/2/2_add_two_numbers.svg)

*对两数相加方法的可视化: 342 + 465 = 807，每个结点都包含一个数字，并且数字按位逆序存储。*

首先从最低有效位也就是列表 `l1` 和 `l2` 的表头开始相加。由于每位数字都应当处于 `0…9` 的范围内，我们计算两个数字的和时可能会出现 “溢出”。例如，`5 + 7 = 12` 。在这种情况下，我们会将当前位的数值设置为 `2`，并将进位 `carry = 1` 带入下一次迭代。进位 `carry` 必定是 `0` 或 `1`，这是因为两个数字相加（考虑到进位）可能出现的最大和为 `9 + 9 + 1 = 19`。

请特别注意以下情况：

| 测试用例                 | 说明                                               |
| ------------------------ | -------------------------------------------------- |
| *l*1=[0,1]，*l*2=[0,1,2] | 当一个列表比另一个列表长时                         |
| *l*1=[]，*l*2=[0,1]      | 当一个列表为空时，即出现空列表                     |
| *l*1=[9,9]，*l*2=[1]     | 求和运算最后可能出现额外的进位，这一点很容易被遗忘 |



```java
    public class ListNode {
        int val;
        ListNode next;
        ListNode(int x) {
            val = x;
        }
    }

    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummyHead = new ListNode(0);//存放结果 存在头结点 0
        ListNode p = l1, q = l2, curr = dummyHead;
        int carry = 0;//是否有进位 1 表示 有
        while (p != null || q != null) {
            int x = (p != null) ? p.val : 0;
            int y = (q != null) ? q.val : 0;
            int sum = carry + x + y;//计算当前位数的和
            carry = sum / 10;//判断是否需要进位
            curr.next = new ListNode(sum % 10);//添加一位 % 10 防止有进位
            curr = curr.next;//移到刚刚设好值的那一位
            if (p != null) p = p.next;
            if (q != null) q = q.next;
        }
        if (carry > 0) {
            curr.next = new ListNode(carry);
        }
        return dummyHead.next;
    }
```

复杂度分析

时间复杂度：*O*(max(m, n))，假设 `m` 和 `n` 分别表示 `l1` 和 `l2` 的长度，上面的算法最多重复 max(m, n) 次。

空间复杂度：*O*(max(m, n))， 新列表的长度最多为 max(m,n) + 1。

