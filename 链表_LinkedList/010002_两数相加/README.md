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

## 方法一 循环



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
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        ListNode prev = head;
        int carry = 0;
        while (l1 != null || l2 != null || carry != 0) {
            int sum = (l1 != null ? l1.val : 0) + (l2 != null ? l2.val : 0) + carry;//求两个节点相加的值
            carry = sum / 10;//取进位的值
            prev.next = new ListNode(sum % 10);//sum对10求余后放到节点中
            prev = prev.next;
            l1 = l1 != null ? l1.next : l1;
            l2 = l2 != null ? l2.next : l2;
        }
        return head.next;
    }
```

复杂度分析

时间复杂度：*O*(max(m, n))，假设 `m` 和 `n` 分别表示 `l1` 和 `l2` 的长度，上面的算法最多重复 max(m, n) 次。

空间复杂度：*O*(max(m, n))， 新列表的长度最多为 max(m,n) + 1。

## 方法二 递归

```java
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        helper(head, l1, l2, 0);
        return head.next;
    }

    private void helper(ListNode result, ListNode l1, ListNode l2, int carry) {
        if (l1 == null && l2 == null && carry == 0)
            return;
        int sum = (l1 != null ? l1.val : 0) + (l2 != null ? l2.val : 0) + carry;
        result.next = new ListNode(0);
        result.next.val = sum % 10;
        carry = sum / 10;
        helper(result.next, l1 != null ? l1.next : null, l2 != null ? l2.next : null, carry);
    }

```

