给你两个链表 `list1` 和 `list2` ，它们包含的元素分别为 `n` 个和 `m` 个。

请你将 `list1` 中第 `a` 个节点到第 `b` 个节点删除，并将`list2` 接在被删除节点的位置。

下图中蓝色边和节点展示了操作后的结果：

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/11/28/fig1.png)

请你返回结果链表的头指针。

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/11/28/merge_linked_list_ex1.png)

```
输入：list1 = [0,1,2,3,4,5], a = 3, b = 4, list2 = [1000000,1000001,1000002]
输出：[0,1,2,1000000,1000001,1000002,5]
解释：我们删除 list1 中第三和第四个节点，并将 list2 接在该位置。上图中蓝色的边和节点为答案链表。
```

**示例 2：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2020/11/28/merge_linked_list_ex2.png)

```
输入：list1 = [0,1,2,3,4,5,6], a = 2, b = 5, list2 = [1000000,1000001,1000002,1000003,1000004]
输出：[0,1,1000000,1000001,1000002,1000003,1000004,6]
解释：上图中蓝色的边和节点为答案链表。
```

**提示：**

- `3 <= list1.length <= 104`
- `1 <= a <= b < list1.length - 1`
- `1 <= list2.length <= 104`

# 题解

```java
    public ListNode mergeInBetween(ListNode list1, int a, int b, ListNode list2) {
        ListNode cur = list1;
        int count = b - a + 2;
        // 找到 list1 中第 a 个节点的前一个节点
        while ((a - 1) > 0) {
            a--;
            cur = cur.next;
        }
        ListNode left = cur;
        // 找到 list1 中第 b 个节点的后一个节点
        while (count > 0) {
            cur = cur.next;
            count--;
        }
        ListNode right = cur;
        // 将 list1 中第 a 个节点的前一个节点指向 list2 的头节点
        left.next = list2;
        // 找到 list2 的尾节点
        while (list2.next != null) {
            list2 = list2.next;
        }
        // 将 list2 的尾节点指向 list1 中第 b 个节点的后一个节点
        list2.next = right;
        return list1;
    }
```













