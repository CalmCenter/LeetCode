## 题目

反转从位置 *m* 到 *n* 的链表。请使用一趟扫描完成反转。

**说明:**
1 ≤ *m* ≤ *n* ≤ 链表长度。

**示例1:**

```
输入: 1->2->3->4->5->NULL, m = 2, n = 4
输出: 1->4->3->2->5->NULL
```

## 解法一 递归指针回溯

使用三个参数: `m` 为反转的起点, `n` 为反转的终点, 以及从第`n` 个结点开始，随着递归回溯过程向左移动的指针 `right` 

此外，我们还有一个指针 `left`，它从第 `m` 个结点开始向前移动。

```
 left
  ↓
  1 -> 2(m) -> 3 -> 4(n) -> 5
  ↑                 
right
```

`left` 每次向右移动时 `m-1` 直到 `m=1` 的时候，说明 `left` 指向反转链表第一个节点；
`right` 每次向右移动时 `n-1` 直到 `n=1` 的时候，说明 `right` 指向反转链表末尾，然后开始 回溯。

```
     left
       ↓
  1 -> 2 -> 3 -> 4 -> 5
                 ↑                 
                right
```

开始置换数据，并将 `left` 指针前移：`left = left.next`。我们需要此变化在回溯过程中保持，所以 `left` 是一个全局变量，回溯过程中，`right` 会向左移动。

```
           left
            ↓
  1 -> 4 -> 3 -> 2 -> 5
            ↑                 
          right
```

当 `right == left` 或者 `right.next == left` 时停止交换。当子链表的长度为奇数时，情况为前者；当子链表长度为偶数时为后者。

代码：

```java
    public void recurseAndReverse(ListNode right, int m, int n) {
        // 递归结束条件，右指针指到需要反转的末尾，也就是 n = 1 的时候，开始回溯
        if (n == 1) {
            return;
        }
        // 右指针向右移一位
        right = right.next;
        // 左指针指到反转的开头
        if (m > 1) {
            this.left = this.left.next;
        }
        // 递归让 m 和 n 减少
        this.recurseAndReverse(right, m - 1, n - 1);
        // 左右指针 从 反转数据的 最外层 向里移动
        // 左指针手动移动， 右指针通过递归 right 引用会回溯

        // 反转数据奇数情况：如果 左右指针相同，证明反转完毕
        //         偶数情况：如果 左右指针交叉，右指针在左指针左面，证明反转完成
        if (this.left == right || right.next == this.left) {
            this.stop = true;
        }
        // 是否需要交换数据
        if (!this.stop) {
            //将左右指针的数据交换
            int t = this.left.val;
            this.left.val = right.val;
            right.val = t;
            // 左指针向 右 移动一位
            this.left = this.left.next;
        }
    }

    public ListNode reverseBetween(ListNode head, int m, int n) {
        this.left = head;
        this.stop = false;
        this.recurseAndReverse(head, m, n);
        return head;
    }
```

复杂度分析

时间复杂度: O(N)。对每个结点最多处理两次。递归过程，在回溯过程中，我们只交换了一半的结点，总复杂度是 O(N)。
空间复杂度: 最坏情况下为 O(N)。在最坏的情况下，我们需要反转整个链表。这是此时递归栈的大小。

## 解法二 迭代链接反转

`head` 表示需要反转的头节点，`pre` 表示需要反转头节点的前驱节点

```
      pre
       ↓   
  0 -> 1 -> 2(m) -> 3 -> 4(n) -> 5
             ↑      ↑           
            head   next
```

我们需要反转 `n-m` 次，我们将 `head` 的 `next` 节点移动到需要反转链表部分的首部，需要反转链表部分剩余节点依旧保持相对顺序即可

```java
ListNode next = head.next; // next = 3
head.next = head.next.next; // 2 -> 4
next.next = prev.next; // 3 -> 2
prev.next = next; // pre -> 3

结果：

0 -> 1 -> 3 -> 2 -> 4 -> 5

0 -> 1(pre) -> 3 -> 2(head) -> 4(next) -> 5

ListNode next = head.next; // next = 4
head.next = head.next.next; // 2 -> 5
next.next = prev.next; // 4 -> 3
prev.next = next; // pre -> 4

结果：

0 -> 1 -> 4 -> 3 -> 2 -> 5
```

这样中间部分 `234` 就变成了 `432` 。

```java
    public ListNode reverseBetween3(ListNode head, int m, int n) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy;
        while (m > 1) {
            prev = prev.next;
            m--;
            n--;
        }
        head = prev.next;
        ListNode next;
        while (n > 1) {
            next = head.next;
            head.next = head.next.next;
            next.next = prev.next;
            prev.next = next;
            n--;
        }
        return dummy.next;
    }
```

时间复杂度: O(N)。考虑包含 N 个结点的链表。对每个节点最多会处理

空间复杂度: O(1)。我们仅仅在原有链表的基础上调整了一些指针，只使用了 O(1) 的额外存储空间来获得结果。

