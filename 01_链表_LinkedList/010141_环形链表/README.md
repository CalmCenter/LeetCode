## 题目

给定一个链表，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 `next` 指针再次到达，则链表中存在环。 为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 `0` 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。注意：`pos` 不作为参数进行传递，仅仅是为了标识链表的实际情况。

如果链表中存在环，则返回 `true` 。 否则，返回 `false` 。

进阶：

你能用 `O(1)`（即，常量）内存解决此问题吗？

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

**提示：**

- 链表中节点的数目范围是 `[0, 10^4]` 
- `-10^5 <= Node.val <= 10^5` 
- `pos` 为 `-1` 或者链表中的一个 **有效索引** 。

## 解法 快慢指针

结束条件是如果不存在环，则返回 `false`，如果存在环则会出现 `fast = slow` 的情况，则返回 `true`。

```java
public boolean hasCycle(ListNode head) {
    if (head == null || head.next == null) {
        return false;
    }
    ListNode slow = head;
    ListNode fast = head.next;
    while (slow != fast) {
        if (fast == null || fast.next == null) {
            return false;
        }
        slow = slow.next;
        fast = fast.next.next;
    }
    return true;
}
```

时间复杂度：O(N)，其中 N 是链表中的节点数。

- 当链表中不存在环时，快指针将先于慢指针到达链表尾部，链表中每个节点至多被访问两次。

- 当链表中存在环时，每一轮移动后，快慢指针的距离将减小一。而初始距离为环的长度，因此至多移动 N 轮。

空间复杂度：O(1)。我们只使用了两个指针的额外空间。


## 解法 哈希表

```java

public boolean hasCycle(ListNode head) {
    Set<ListNode> seen = new HashSet<ListNode>();
    while (head != null) {
        if (!seen.add(head)) {
            return true;
        }
        head = head.next;
    }
    return false;
}

```

复杂度分析

时间复杂度：O(N)，其中 N 是链表中的节点数。最坏情况下我们需要遍历每个节点一次。

空间复杂度：O(N)，其中 N 是链表中的节点数。主要为哈希表的开销，最坏情况下我们需要将每个节点插入到哈希表中一次。

