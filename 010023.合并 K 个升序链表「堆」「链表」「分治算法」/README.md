## 题目

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

**示例1:**

```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

**示例2:**

```
输入：lists = []
输出：[]
```

**示例3:**

```
输入：lists = [[]]
输出：[]
```

**提示：**

```
k == lists.length
0 <= k <= 10^4
0 <= lists[i].length <= 500
-10^4 <= lists[i][j] <= 10^4
lists[i] 按 升序 排列
lists[i].length 的总和不超过 10^4
```



## 解题

### 方法  1 ：指针

因为每个队列是有序的，我们可以将每个队列中的第一个进行对比，并取出最小的，然后在链表中删除这个节点。以此类推

```java
    public ListNode mergeKLists(ListNode[] lists) {
        int k = lists.length;
        ListNode dummyHead = new ListNode(0);
        ListNode tail = dummyHead;
        while (true) {
            ListNode minNode = null;
            int minPointer = -1;
            //循环数组
            for (int i = 0; i < k; i++) {
                if (lists[i] == null) {
                    continue;
                }
                //因为是有序的，所以取出 所有链表第一个值，比较出最小的，并标记是哪个 链表
                if (minNode == null || lists[i].val < minNode.val) {
                    minNode = lists[i];
                    minPointer = i;
                }
            }
            if (minPointer == -1) {
                break;
            }
            //将 最小的节点添加到  输出链表
            tail.next = minNode;
            tail = tail.next;
            //将标记的链表中的第一个节点删除
            lists[minPointer] = lists[minPointer].next;
        }
        return dummyHead.next;
    }
```

 时间复杂度：O( N K )：while 循环每次只能确定一个节点，确定每个节点需要 for 循环 K 次，所以 N K。

---

### 方法  2 ：优先级队列（通过 `PriorityQueue` 实现小根堆）

通过 `PriorityQueue` 实现小根堆，优化方法 `1` 中每个节点 K 次循环才能找出最小节点的过程。

[刷算法不知道PriorityQueue?看了这篇文章才知道他有多实用](https://baijiahao.baidu.com/s?id=1665383380422326763&wfr=spider&for=pc)

> `PriorityQueue` 类在 `Java1.5` 中引入。`PriorityQueue` 是基于优先堆的一个无界队列，这个优先队列中的元素可以默认自然排序或者通过提供的`Comparator`（比较器）在队列实例化的时排序。要求使用 `Java Comparable` 和 `Comparator` 接口给对象排序，并且在排序时会按照优先级处理其中的元素。

```java
    public ListNode mergeKLists(ListNode[] lists) {
        //PriorityQueue 小根堆
        Queue<ListNode> pq = new PriorityQueue<>((v1, v2) -> v1.val - v2.val);
        for (ListNode node : lists) {
            if (node != null) {
                pq.offer(node);
            }
        }
        ListNode dummyHead = new ListNode(0);
        ListNode tail = dummyHead;
        while (!pq.isEmpty()) {
            //会poll  出 第一个节点 最小的队列
            ListNode minNode = pq.poll();
            tail.next = minNode;
            tail = minNode;
            if (minNode.next != null) {
                //将poll出的队列去掉第一个节点重新添加到 queue 中
                pq.offer(minNode.next);
            }
        }
        return dummyHead.next;
    }
```

时间复杂度：O(N log K )：while 循环每次只能确定一个节点 N 表示所有节点，考虑优先队列中的元素不超过 k 个，那么插入和删除的时间代价为 *O*(log*k*)比较出最小的节点

---

### 方法  3 ：逐一合并两条链表

[010021.合并两个有序链表「链表」]([https://github.com/CalmCenter/LeetCode/tree/master/010021.%E5%90%88%E5%B9%B6%E4%B8%A4%E4%B8%AA%E6%9C%89%E5%BA%8F%E9%93%BE%E8%A1%A8%E3%80%8C%E9%93%BE%E8%A1%A8%E3%80%8D](https://github.com/CalmCenter/LeetCode/tree/master/010021.合并两个有序链表「链表」))

合并两条有序链表 — 递归

```java
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) {
            return l2;
        } else if (l2 == null) {
            return l1;
        } else if (l1.val < l2.val) {
            // l1 < l2
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
```

合并两条有序链表 — 迭代

```java
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode merge = new ListNode(0);
        ListNode dummy = merge;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                merge.next = l1;
                l1 = l1.next;
            } else {
                merge.next = l2;
                l2 = l2.next;
            }
            merge = merge.next;
        }
        merge.next = l1 == null ? l2 : l1;
        return dummy.next;
    }
```

逐一合并两条链表

```java
    public ListNode mergeKLists(ListNode[] lists) {
        ListNode res = null;
        for (ListNode list: lists) {
            res = mergeTwoLists(res, list);
        }
        return res;
    }
```

时间复杂度： O(k^2 n) ：假设每个链表的最长长度是 n。在第一次合并后，ans 的长度为 n；第二次合并后，ans 的长度为 2×n，第 i 次合并后，ans 的长度为 i×n。第 i 次合并的时间代价是 O(n+(i−1)×n)=O(i×n)，那么总的时间代价为 O(n + 2n + 3n + … + (k-1)n + kn)=O((1+k)⋅k/2 ×n)=O(k^2 n)，故渐进时间复杂度为 O(k^2 n)

---

### 方法  3 ：分治算法（两两合并）

两两合并 - 迭代

```java
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) {
            return null;
        }
        int k = lists.length;
        while (k > 1) {
            int idx = 0;
            for (int i = 0; i < k; i += 2) {
                if (i == k - 1) {
                    lists[idx++] = lists[i];
                } else {
                    lists[idx++] = mergeTwoLists(lists[i], lists[i + 1]);
                }
            }
            k = idx;
        }
        return lists[0];
    }
```

两两合并 - 递归

```java
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) {
            return null;
        }
        return merge(lists, 0, lists.length - 1);
    }

    private ListNode merge(ListNode[] lists, int lo, int hi) {
        if (lo == hi) {
            return lists[lo];
        }
        int mid = lo + (hi - lo) / 2;
        ListNode l1 = merge(lists, lo, mid);
        ListNode l2 = merge(lists, mid + 1, hi);
        return mergeTwoLists(l1, l2);
    }
```

时间复杂度：O(N log K)：K 条链表的总节点数是 N，平均每条链表有 N/K 个节点，因此合并两条链表的时间复杂度是 O(N/K)。从 K 条链表开始两两合并成 1 条链表，**因此每条链表都会被合并 log K 次**，因此 K 条链表会被合并 K∗log K 次，因此总共的时间复杂度是 K∗log K∗N/K 即 O(N log K)。

