## 题目

给定一个链表和一个特定值 *x*，对链表进行分隔，使得所有小于 *x* 的节点都在大于或等于 *x* 的节点之前。

你应当保留两个分区中每个节点的初始相对位置。

**示例1:**

```
输入: head = 1->4->3->2->5->2, x = 3
输出: 1->2->2->4->3->5
```

## 解法

1. 初始化两个指针 `before` 和 `after`。在实现中，我们将两个指针初始化为哑 `ListNode`。这有助于减少条件判断。

```
  1 -> 4 -> 3 -> 2 -> 5 -> 2
  ↑                 
 head
  0 -> 
  ↑                 
befor
  0 -> 
  ↑                 
after
```

2. 利用`head`指针遍历原链表。
3. 若`head` 指针指向的元素值 *小于* `x`，该节点应当是 `before` 链表的一部分。因此我们将其移到 `before` 中。否则，该节点应当是`after` 链表的一部分。因此我们将其移到 `after` 中。

```
  1 -> 4 -> 3 -> 2 -> 5 -> 2
                      ↑                 
                    head
  0 -> 1 -> 2 
            ↑                 
          befor
  0 -> 4 -> 3
            ↑                 
          after
```

4. 遍历完原有链表的全部元素之后，我们得到了两个链表 `before` 和 `after`。原有链表的元素或者在`before` 中或者在 `after` 中，这取决于它们的值。

```
  0 -> 1 -> 2 -> 2
                 ↑                 
               befor
  0 -> 4 -> 3 -> 5
                 ↑                 
               after
```

5. 现在，可以将 `before` 和 `after` 连接，组成所求的链表。

```
  0 -> 1 -> 2 -> 2  ->  4 -> 3 -> 5
                 ↑      ↑         ↑                 
              befor  after_head  after

```

代码：

```java
    public ListNode partition(ListNode head, int x) {
        // 初始化两个链表
        ListNode before_head = new ListNode(0);
        ListNode before = before_head;
        ListNode after_head = new ListNode(0);
        ListNode after = after_head;

        while (head != null) {
            // 将小于 x 的节点，链接到 before 后
            if (head.val < x) {
                before.next = head;
                before = before.next;
            } else {
                after.next = head;
                after = after.next;
            }
            // 推进原始 链表
            head = head.next;
        }
        after.next = null;
        //合并两个链表
        before.next = after_head.next;
        return before_head.next;
    }
```

复杂度分析

时间复杂度: O(N)，其中 N 是原链表的长度，我们对该链表进行了遍历。
空间复杂度: O(1)，我们没有申请任何新空间。值得注意的是，我们只移动了原有的结点，因此没有使用任何额外空间。

