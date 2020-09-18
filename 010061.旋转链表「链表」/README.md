## 题目

给定一个链表，旋转链表，将链表每个节点向右移动 *k* 个位置，其中 *k* 是非负数。

**示例1:**

```
输入: 1->2->3->4->5->NULL, k = 2
输出: 4->5->1->2->3->NULL
解释:
向右旋转 1 步: 5->1->2->3->4->NULL
向右旋转 2 步: 4->5->1->2->3->NULL
```

**示例 2:**

```
输入: 0->1->2->NULL, k = 4
输出: 2->0->1->NULL
解释:
向右旋转 1 步: 2->0->1->NULL
向右旋转 2 步: 1->2->0->NULL
向右旋转 3 步: 0->1->2->NULL
向右旋转 4 步: 2->0->1->NULL
```

## 解题

首先将链表闭合成环，在闭合时我们还可以记录节点个数 `n`

然后找到相应的位置断开这个环，确定新的链表头和链表尾

```java
   1 -> 2 -> 3 -> 4 -> 5  
   ↑                   ↓
   ← ← ← ← ← ← ← ← ← ← ←
```

如何找到找到需要断开的位置

右移 `k` 位，证明最后一个节点（ next 需要断开的节点）会变成第 `(n - k)` 个，变成下标就是 `(n - k) - 1` 。

但是 `k` 可能大于 `n` ，`k` 可以写成 `k / n * n + k % n`  

`k / n * n` 表示正好整圈的个数（相当于无意义的移动，因为是整圈，最终也会绕回原点）

`k % n` 表示最终有意义的移动个数

所以可以写成 ` n - k % n -1`

```java
    public ListNode rotateRight(ListNode head, int k) {
        // base cases
        if (head == null) return null;
        if (head.next == null) return head;

        // 找到尾节点形成环形链表，并且记录节点总数 n
        ListNode old_tail = head;
        int n;
        for(n = 0; old_tail.next != null; n++)
            old_tail = old_tail.next;
        old_tail.next = head;

        // 找到新的尾节点 : (n - k % n - 1)
        // 找到新的头节点 : (n - k % n)
        ListNode new_tail = head;
        for (int i = 0; i < n - k % n; i++)
            new_tail = new_tail.next;
        ListNode new_head = new_tail.next;

        // 断开新的尾结点
        new_tail.next = null;

        return new_head;
    }
```

**复杂度分析**

- 时间复杂度：*O*(*N*)，其中 *N* 是链表中的元素个数
- 空间复杂度：*O*(1)，因为只需要常数的空间

