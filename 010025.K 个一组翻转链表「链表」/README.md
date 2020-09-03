## 题目

给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。

k 是一个正整数，它的值小于或等于链表的长度。

如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

**示例1:**

```
给你这个链表：1->2->3->4->5

当 k = 2 时，应当返回: 2->1->4->3->5

当 k = 3 时，应当返回: 3->2->1->4->5
```

**说明：**

- 你的算法只能使用常数的额外空间。
- **你不能只是单纯的改变节点内部的值**，而是需要实际进行节点交换。

## 解题

### 方法  1 ：指针

链表分区为  已翻转部分+待翻转部分+未翻转部分 

`k = 3` 时

```java
       ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode pre = dummy;
        ListNode end = dummy;

        while (end.next != null) {
            for (int i = 0; i < k && end != null; i++) end = end.next;
            if (end == null) break;
            ListNode start = pre.next;
            ListNode next = end.next;
        ...
```

特殊情况：当翻转部分长度不足 `k` 时，在定位 `end` 完成后，`end==null`，已经到达末尾，说明题目已完成(如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。)

数据表示：`pre` 表示待翻转部分的前一个节点，`start` 到 `end` 之间为待翻转部分的 `k` 个节点，`next` 为未翻转部分的头节点。

```java
dummy   1   2   3   4   5
  ↑     ↑       ↑   ↑  
 pre  start    end next
```

`end.next = null;` 这句话将 待翻转部分 单独提出来，`start` 为头节点的链表中只会有 k 个 待翻转 的节点。

`pre.next = reverse(start);` 执行后将 翻转后的 `k` 个节点连到 `dummy` 。

当前数据表示：当前其实有两段链表 分别为 `pre` 表示的已翻转部分，和 `next` 表示的为翻转部分。

```java
dummy   3   2   1   
  ↑     ↑       ↑    
 pre   end    start
 
 4   5
 ↑
next
```

`start.next = next;`  就是将两段断裂的链表连接起来。然后更新 `pre` 与 `end` 的值

完整代码：

```java
    public ListNode reverseKGroup(ListNode head, int k) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        //pre 表示 待翻转组 的 前一个节点
        ListNode pre = dummy;
        //end 表示待翻转部分最后一个节点
        ListNode end = dummy;

        while (end.next != null) {
            //将 end 移动到待翻转部分最后一个节点
            for (int i = 0; i < k && end != null; i++) end = end.next;
            //如果剩余长度不满 K 位，不做翻转直接返回
            if (end == null) break;
            //start 为待翻转部分第一个节点
            ListNode start = pre.next;
            //next 为未翻转部分的头节点
            ListNode next = end.next;
            //将 start -> end 与 next 到结尾分开，单独提出 start -> end 中的节点
            end.next = null;
            //翻转 start -> end 中的节点
            pre.next = reverse(start);
            //翻转后 start 为本组最后一个节点，连接 下一组第一个节点 next
            start.next = next;
            //更新 pre ，pre 表示 待翻转组 的 前一个节点
            pre = start;

            end = pre;
        }
        return dummy.next;
    }

    private ListNode reverse(ListNode head) {
        ListNode pre = null;
        ListNode curr = head;
        //多个节点翻转的方法，每次记录当前节点，为一下个节点的 next
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = pre;
            pre = curr;
            curr = next;
        }
        return pre;
    }

```

复杂度分析

时间复杂度为 ：O(n *K*) 最好的情况为 *O*(*n*) 最差的情况未 O(n^2)

空间复杂度为 ：O(1)