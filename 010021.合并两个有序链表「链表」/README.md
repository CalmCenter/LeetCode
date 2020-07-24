## 题目

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**示例1:**

```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

## 解题

### 方法  1 ：暴力循环法

```java
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode merge = new ListNode(0);
        ListNode dummy = merge;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                merge.next = l1;
                l1 = l1.next;
            } else {
                merge.next = l2;
                l2 = l2.next;
            }
            merge = merge.next;
        }
        merge.next = l1 == null ? l2 : l1;
        return dummy.next;
    }
```

时间复杂度：O(n + m) 其中 n 和 m 分别为两个链表的长度。因为每次循环迭代中，`l1` 和 `l2` 只有一个元素会被放进合并链表中， 因此 `while` 循环的次数不会超过两个链表的长度之和。所有其他操作的时间复杂度都是常数级别的，因此总的时间复杂度为 O(n+m)

空间复杂度：O(1)

### 方法  2 ：递归

如果 `l1` 或者 `l2` 一开始就是空链表 ，那么没有任何操作需要合并，所以我们只需要返回非空链表。
否则，我们要判断 `l1` 和 `l2` 哪一个链表的头节点的值更小，然后递归地决定下一个添加到结果里的节点。如果两个链表有一个为空，递归结束。

```java
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        } else if (l2 == null) {
            return l1;
        } else if (l1.val < l2.val) {
            // l1 < l2
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
```


复杂度分析

时间复杂度：O(n + m)，其中 n 和 m 分别为两个链表的长度。因为每次调用递归都会去掉 `l1` 或者 `l2` 的头节点（直到至少有一个链表为空），函数 `mergeTwoList` 至多只会递归调用每个节点一次。因此，时间复杂度取决于合并后的链表长度，即 O(n+m)

空间复杂度：O(n + m)，其中 n 和 m 分别为两个链表的长度。递归调用 `mergeTwoLists` 函数时需要消耗栈空间，栈空间的大小取决于递归调用的深度。结束递归调用时 `mergeTwoLists` 函数最多调用 n+m 次，因此空间复杂度为 O(n+m)

