## 题目

编写代码，移除未排序链表中的重复节点。保留最开始出现的节点。

**示例1:**

```
 输入：[1, 2, 3, 3, 2, 1]
 输出：[1, 2, 3]
```

**示例2:**

```
 输入：[1, 1, 1, 1, 2]
 输出：[1, 2]
```

**提示：**

1. 链表长度在 [0, 20000] 范围内。
2. 链表元素在 [0, 20000] 范围内。

## 方法一：哈希表

我们对给定的链表进行一次遍历，并用一个哈希集合（`HashSet`）来存储所有出现过的节点。

```java
    public ListNode removeDuplicateNodes(ListNode head) {
        if (head == null) {
            return head;
        }
        Set<Integer> occurred = new HashSet<Integer>();
        occurred.add(head.val);
        ListNode pos = head;
        // 枚举前驱节点
        while (pos.next != null) {
            // 当前待删除节点
            ListNode cur = pos.next;
            if (occurred.add(cur.val)) {
                pos = pos.next;
            } else {
                pos.next = pos.next.next;
            }
        }
        return head;
    }

```

**复杂度分析**

时间复杂度：O(N)，其中 N 是给定链表中节点的数目。

空间复杂度：O(N)。在最坏情况下，给定链表中每个节点都不相同，哈希表中需要存储所有的 N 个值。



