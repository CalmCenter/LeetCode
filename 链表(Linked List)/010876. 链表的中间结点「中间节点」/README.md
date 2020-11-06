## 题目

给定一个头结点为 head 的非空单链表，返回链表的中间结点。

如果有两个中间结点，则返回第二个中间结点。

示例 1：

```
输入：[1,2,3,4,5]
输出：此列表中的结点 3 (序列化形式：[3,4,5])
返回的结点值为 3 。 (测评系统对该结点序列化表述是 [3,4,5])。
注意，我们返回了一个 ListNode 类型的对象 ans，这样：
ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, 以及 ans.next.next.next = NULL.
```

示例 2：

```
输入：[1,2,3,4,5,6]
输出：此列表中的结点 4 (序列化形式：[4,5,6])
由于该列表有两个中间结点，值分别为 3 和 4，我们返回第二个结点。
```


提示：

给定链表的结点数介于 1 和 100 之间。

## 解法一：数组

链表的缺点在于不能通过下标访问对应的元素。我们可以把链表中的数据遍历复制到数组中，然后访问第 `n/2` 个

```java
    public ListNode middleNode(ListNode head) {
        ListNode[] A = new ListNode[100];
        int n = 0;
        while (head != null) {
            A[n++] = head;
            head = head.next;
        }
        return A[n / 2];
    }
```

**复杂度分析**

- 时间复杂度：*O*(*N*)，其中 *N* 是给定链表中的结点数目。
- 空间复杂度：*O*(*N*)，即数组 `A` 用去的空间。

## 方法二：快慢指针法

`slow` 一次走一步，`fast` 一次走两步。那么当 `fast` 到达链表的末尾时，`slow` 必然位于中间。

```java
    public ListNode middleNode(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }

```

复杂度分析

时间复杂度：O(N)，其中 N 是给定链表的结点数目。

空间复杂度：O(1)，只需要常数空间存放 slow 和 fast 两个指针。

