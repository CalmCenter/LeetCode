## 题目

给你一个链表的头节点 head，请你编写代码，反复删去链表中由 总和 值为 0 的连续节点组成的序列，直到不存在这样的序列为止。

删除完毕后，请你返回最终结果链表的头节点。

你可以返回任何满足题目要求的答案。

（注意，下面示例中的所有序列，都是对 ListNode 对象序列化的表示。）

**示例 1：**

```
输入：head = [1,2,-3,3,1]
输出：[3,1]
提示：答案 [1,2,1] 也是正确的。
```

**示例 2：**

```
输入：head = [1,2,3,-3,4]
输出：[1,2,4]
```

**示例 3：**

```
输入：head = [1,2,3,-3,-2]
输出：[1]
```

**提示：**

- 给你的链表中可能有 `1` 到 `1000` 个节点。
- 对于链表中的每个节点，节点的值：`-1000 <= node.val <= 1000`.

## 题解：

```
       1  2  3  -1  -4  3  4
 sum:  1  3  6   5   1  4  8
       ↑             ↑
       d          map.get(sum)
```

通过下面的代码，将和重复的节点跳过

```
    for (ListNode d = dummy; d != null; d = d.next) {
        sum += d.val;
        d.next = map.get(sum).next;
    }
```



```java
public ListNode removeZeroSumSublists(ListNode head) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;

    Map<Integer, ListNode> map = new HashMap<>();

    // 首次遍历建立 节点处链表和<->节点 哈希表
    // 若同一和出现多次会覆盖，即记录该sum出现的最后一次节点
    int sum = 0;
    for (ListNode d = dummy; d != null; d = d.next) {
        sum += d.val;
        map.put(sum, d);
    }

    // 第二遍遍历 若当前节点处sum在下一处出现了则表明两结点之间所有节点和为0 直接删除区间所有节点
    sum = 0;
    for (ListNode d = dummy; d != null; d = d.next) {
        sum += d.val;
        d.next = map.get(sum).next;
    }

    return dummy.next;
}
```