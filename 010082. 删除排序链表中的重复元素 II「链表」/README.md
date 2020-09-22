## 题目

给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中 *没有重复出现* 的数字。

**示例1:**

```
输入: 1->2->3->3->4->4->5
输出: 1->2->5
```

**示例 2:**

```
输入: 1->1->1->2->3
输出: 2->3
```

## 解法一 双指针

使用双指针的方式，定义 `a`，`b` 两个指针

考虑到一些边界条件，比如 `1->1->1->2` 这种情况，需要把开头的几个 `1` 给去掉，我们增加一个**哨兵节点**，方便边界处理，`a` 指针必须指向要处理数据的前一个节点。

- 将`a`指针指向哨兵节点
- 将`b`指针指向`head.next` ( 需要比较的节点 )

如果`a` 的 `next`  指向的值**不等于** `b` 指向的值，则两个指针都前进一位
否则，就单独移动 `b`，`b` 不断往前走，直到`a` 的 `next`   指向的值**不等于**`b`指向的值。

这样，`a.next`  到 `b` 的前一个节点都是重复的节点，只需要 `a.next = b` 就可以删除重复的。

```java
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode a = dummy;
        ListNode b = head.next;
        while (b != null) {
            if (a.next.val != b.val) {
                a = a.next;
            } else {
                //如果 a.next、b 指向的节点值相等，就不断移动 b，直到 a.next、b 指向的值不相等
                while (b != null && a.next.val == b.val) {
                    b = b.next;
                }
                //删除 a 到 b 之间的节点
                a.next = b;
            }
            // b指针在 while 中判断完后，可能指向了null，这里需要处理边界问题
            b = (b == null) ? null : b.next;
        }
        return dummy.next;
    }

```



## 解法二 递归

思路：删除所有头部的重复节点，返回不重复的第一个节点。

1.特殊情况，头节点为null或头节点下一节点为null，直接返回头节点，这时不存在重复节点
2.如果头节点与，头节点的下一节点值相等，跳过所有相等节点。递归调用函数判断最后一个跳过节点的后一节点。
3.如果头节点与，头节点的下一节点值不等，递归调用函数判断头节点的后一节点。

```java
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        //如果 当前节点与下一个节点相同
        if (head.val == head.next.val) {
            while (head.next != null && head.val == head.next.val) {
                // 跳过所有相同的节点
                head = head.next;
            }
            // 继续判断下一个 不同的节点
            return deleteDuplicates(head.next);
        } else {
            // 将所有不同的节点连接在一起
            head.next = deleteDuplicates(head.next);
            return head;
        }
    }
```



