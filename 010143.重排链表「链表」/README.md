## 题目

- 给定一个单链表 L：L0→L1→…→Ln-1→Ln ，
  将其重新排列后变为： L0→Ln→L1→Ln-1→L2→Ln-2→…

  你不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。


**示例 1:**

```
给定链表 1->2->3->4, 重新排列为 1->4->2->3.
```

**示例 2:**

```
给定链表 1->2->3->4->5, 重新排列为 1->5->2->4->3.
```

## 方法一：线性表

因为链表不支持下标访问，所以我们无法随机访问链表中任意位置的元素。

利用线性表存储该链表，然后利用线性表可以下标访问的特点，直接按顺序访问指定元素，重建该链表。

```java
class Solution {
    public void reorderList(ListNode head) {
        if (head == null) {
            return;
        }
        List<ListNode> list = new ArrayList<ListNode>();
        ListNode node = head;
        while (node != null) {
            list.add(node);
            node = node.next;
        }
        int i = 0, j = list.size() - 1;
        while (i < j) {
            list.get(i).next = list.get(j);
            i++;
            if (i == j) {
                break;
            }
            list.get(j).next = list.get(i);
            j--;
        }
        list.get(i).next = null;
    }
}
```

**复杂度分析**

- 时间复杂度：O(N)，其中 N 是链表中的节点数。
- 空间复杂度：O(N)，其中 N 是链表中的节点数。主要为线性表的开销。





## 方法二：寻找链表中点 + 链表逆序 + 合并链表

1. 首先用 快慢指针 寻找中间点，将链表分为前后两端
2. 反转后半部分链表

```
l1 : 1 2 3
l2 : 6 5 4
```

3. 合并

```
l1 : 1
l2 : 6
l1_temp : 2
l2_temp : 5

l1 -> l2       1 -> 6
l1 = l1_temp   l1 : 2

l2 -> l1       6 -> 2
l2 = l2_temp   l2 : 5

循环一次结果 ：1 -> 6 -> 2 -> 3      5 -> 4 

l1 : 2
l2 : 5
l1_temp : 3
l2_temp : 4

l1 -> l2       2 -> 6
l1 = l1_temp   l1 : 3

l2 -> l1       5 -> 3
l2 = l2_temp   l2 : 4

循环第二次结果 ：1 -> 6 -> 2 -> 5 -> 3    4 

l1 : 3
l2 : 4
l1_temp : null
l2_temp : null

l1 -> l2       3 -> 4
l1 = l1_temp   l1 : null

l2 -> l1       4 -> null
l2 = l2_temp   l2 : null

循环第三次结果 ：1 -> 6 -> 2 -> 5 -> 3 -> 4 -> null 
```

代码：

```java

    public void reorderList(ListNode head) {
        if (head == null) {
            return;
        }
        ListNode mid = middleNode(head);
        ListNode l1 = head;
        ListNode l2 = mid.next;
        mid.next = null;
        l2 = reverseList(l2);
        mergeList(l1, l2);
    }

    //取中间点
    public ListNode middleNode(ListNode head) {
        ListNode slow = head;
        ListNode fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }

    //反转后半段链表
    public ListNode reverseList(ListNode head) {
        //记录前一个节点
        ListNode prev = null;
        //当前节点
        ListNode curr = head;
        while (curr != null) {
            //记录下一个节点
            ListNode nextTemp = curr.next;
            //将当前节点指向前一个节点
            curr.next = prev;
            
            //将当前节点变为前一个节点
            prev = curr;
            //将下一个节点变为当前节点
            curr = nextTemp;
        }
        return prev;
    }

    //合并链表
    public void mergeList(ListNode l1, ListNode l2) {
        ListNode l1_tmp;
        ListNode l2_tmp;
        while (l1 != null && l2 != null) {
            l1_tmp = l1.next;
            l2_tmp = l2.next;

            l1.next = l2;
            l1 = l1_tmp;

            l2.next = l1;
            l2 = l2_tmp;
        }
    }
```

**复杂度分析**

- 时间复杂度：O(N)，其中 N 是链表中的节点数。
- 空间复杂度：O(1)。