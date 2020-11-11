## 题目

输入一个链表，输出该链表中倒数第 `k` 个节点。为了符合大多数人的习惯，本题从 `1` 开始计数，即链表的尾节点是倒数第 `1` 个节点。例如，一个链表有 `6` 个节点，从头节点开始，它们的值依次是 `1、2、3、4、5、6` 。这个链表的倒数第 `3` 个节点是值为 `4` 的节点。 

**示例：**

```
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.
```

## 题解：

```
    1 -> 2 -> 3 -> 4 -> 5 -> null   k = 2
    ↑         ↑
  latter    former
```

通过 `while(former != null)` 两个指针同时移动

```
    1 -> 2 -> 3 -> 4 -> 5 -> null   k = 2
                   ↑          ↑
                 latter     former
```

```java
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode former = head, latter = head;
        for(int i = 0; i < k; i++)
            former = former.next;
        while(former != null) {
            former = former.next;
            latter = latter.next;
        }
        return latter;
    }
```

