## 题目

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 `0` 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。注意，`pos` 仅仅是用于标识环的情况，并不会作为参数传递到函数中。

说明：不允许修改给定的链表。

进阶：

你是否可以使用 `O(1)` 空间解决此题？

**提示：**

- 链表中节点的数目范围在范围 `[0, 10^4]` 内
- `-10^5 <= Node.val <= 10^5` 
- `pos` 的值为 `-1` 或者链表中的一个有效索引

## 方法一：哈希表

遍历链表中的每个节点，并将它记录下来；一旦遇到了此前遍历过的节点，就可以判定链表中存在环，并且当前节点就是入环点。

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode pos = head;
        Set<ListNode> visited = new HashSet<ListNode>();
        while (pos != null) {
            if (visited.contains(pos)) {
                return pos;
            } else {
                visited.add(pos);
            }
            pos = pos.next;
        }
        return null;
    }
}
```

复杂度分析

时间复杂度：O(N)，其中 N 为链表中节点的数目。我们恰好需要访问链表中的每一个节点。

空间复杂度：O(N)，其中 N 为链表中节点的数目。我们需要将链表中的每个节点都保存在哈希表当中。

## 方法二：快慢指针

结束条件是如果不存在环，则返回 `null`，如果存在环则会出现 `fast = slow` 的情况。
设链表中环外部分的长度为 `a`。 `slow` 指针进入环后，又走了 `b` 的距离与 `fast` 相遇。此时，`fast` 指针已经走完了环的 `n` 圈，因此它走过的总距离为 `a+n(b+c)+b=a+(n+1)b+nc`。

任意时刻，`fast` 指针走过的距离都为 `slow` 指针的 `2` 倍。而且慢指针入环第一圈没走完的时候就会和快指针相遇，证明 `a+(n+1)b+nc = 2(a+b)`

化简得 `a = c+(n−1)(b+c)` 从 **相遇点到入环点(c)** 的距离加上 **`n−1` 圈(n-1)(b+c)**的环长，恰好等于从 **链表头部到入环点(a)** 的距离。

为什么慢指针入环第一圈没走完的时候就会和快指针相遇

在 `slow` 进入环之后，`fast` 当前不管在环中的哪个结点，都可以在 `slow` 完成一圈之前追上 `slow` ，最极端的情况是 `fast` 刚离开入环点，`slow` 就从那个点进入了环，又因为 `slow` 跑完一圈的时间 `fast` 都跑完两圈了，况且  `fast` 和 `slow` 本来就不在一个起跑线上， `fast` 抢跑了一个结点，那么 `fast`  必然会在 `slow` 跑完一圈前追上 `slow` 。

![图片来源于力扣](https://assets.leetcode-cn.com/solution-static/142/142_fig1.png)

```java
    public ListNode detectCycle(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode slow = head, fast = head;
        while (fast != null) {
            slow = slow.next;
            if (fast.next != null) {
                fast = fast.next.next;
            } else {
                return null;
            }
            if (fast == slow) {
                ListNode ptr = head;
                while (ptr != slow) {
                    ptr = ptr.next;
                    slow = slow.next;
                }
                return ptr;
            }
        }
        return null;
    }
```

复杂度分析

时间复杂度：O(N)，其中 N 为链表中节点的数目。在最初判断快慢指针是否相遇时，slow 指针走过的距离不会超过链表的总长度；随后寻找入环点时，走过的距离也不会超过链表的总长度。因此，总的执行时间为 O(N)+O(N)=O(N)。

空间复杂度：O(1)。我们只使用了 slow,fast,ptr 三个指针。

