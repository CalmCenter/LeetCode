## 题目

给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。

**示例1:**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 *n* 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

## 解题

### 方法  1 ：两次循环

第一次循环获取链表长度

然后用 `length - n` 表示目标节点前面有几个节点。

 第二次循环，找到目标节点的前一个节点，然后删除目标节点。

```java
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        //链表长度
        int length = 0;
        ListNode first = head;
        while (first != null) {
            length++;
            first = first.next;
        }
        //第 length - n 目标节点前面有几个节点。
        length -= n;
        first = dummy;
        while (length > 0) {
            length--;
            first = first.next;
        }
        first.next = first.next.next;
        return dummy.next;
    }
```

**复杂度分析**

时间复杂度：O(L)，该算法对列表进行了两次遍历，首先计算了列表的长度 L 其次找到第 (L−n) 个结点。 操作执行了 2L−n 步，时间复杂度为 O(L)。

空间复杂度：O(1)，我们只用了常量级的额外空间。

### 方法 2 ：一次循环，双指针

我们可以使用两个指针

第一个指针从列表的开头向前移动 n+1 步，而第二个指针将从列表的开头出发

现在，这两个指针被 n 个结点分开。我们通过同时移动两个指针向前来保持这个恒定的间隔，直到第一个指针到达最后一个结点。此时第二个指针将指向从最后一个结点数起的第 n 个结点。

```java
    public ListNode removeNthFromEnd(ListNode head, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode first = dummy;
        ListNode second = dummy;
        for (int i = 0; i <= n; i++) {
            first = first.next;
        }
        while (first != null) {
            first = first.next;
            second = second.next;
        }
        second.next = second.next.next;

        return dummy.next;
    }
```

**复杂度分析**

时间复杂度：O(L)，该算法对含有 L 个结点的列表进行了一次遍历。因此时间复杂度为 O(L)。

空间复杂度：O(1)，我们只用了常量级的额外空间。

