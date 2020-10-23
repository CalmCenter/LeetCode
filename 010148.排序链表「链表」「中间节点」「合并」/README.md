## 题目

在 *O*(*n* log *n*) 时间复杂度和常数级空间复杂度下，对链表进行排序。

示例 1:

```
输入: 4->2->1->3
输出: 1->2->3->4
```

示例 2:

```
输入: -1->5->3->4->0
输出: -1->0->3->4->5
```

## 解法一 归并（递归）

```
                      -1 -> 5 -> 3 -> 4 -> 0 -> 6 -> -2 -> 1
                          /                             \
分割             -1 -> 5 -> 3 -> 4               0 -> 6 -> -2 -> 1
                   /          \                     /           \
               -1 -> 5       3 -> 4              0 -> 6        -2 -> 1
递归终止           |            |                   |             |
               -1 -> 5       3 -> 4              0 -> 6        -2 -> 1
                    \         /                     \             /
合并              -1 -> 3 -> 4 -> 5               -2 -> 0 -> 1 -> 6
                            \                             /
                         -2 -> -1 -> 0 -> 1 -> 3 -> 4 -> 5 -> 6
```

```java
    public ListNode sortList(ListNode head) {
        // 1、递归结束条件
        if (head == null || head.next == null) {
            return head;
        }

        // 2、找到链表中间节点并断开链表 & 递归下探
        ListNode midNode = middleNode(head);
        ListNode rightHead = midNode.next;
        midNode.next = null;

        ListNode left = sortList(head);
        ListNode right = sortList(rightHead);

        // 3、当前层业务操作（合并有序链表）
        return mergeTwoLists(left, right);
    }

    //  找到链表中间节点
    private ListNode middleNode(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode slow = head;
        ListNode fast = head.next.next;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        return slow;
    }

    // 合并两个有序链表
    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode sentry = new ListNode(-1);
        ListNode curr = sentry;
        // l1 l2 链表中 哪个首节点小 将哪个拼接 到 curr 后面。然后 curr 与 被拼接的链表 同时移到下一位
        while(l1 != null && l2 != null) {
            if(l1.val < l2.val) {
                curr.next = l1;
                l1 = l1.next;
            } else {
                curr.next = l2;
                l2 = l2.next;
            }

            curr = curr.next;
        }

        curr.next = l1 != null ? l1 : l2;
        return sentry.next;
    }
```

时间复杂度 ：*O*(*n* log *n*) 

空间复杂度 ：*O*(log *n*)  由于用到了递归。

## 解法二 归并（从底至顶直接合并）

```
mergeNumber=1    5   -1       3    4              6    0        -2    1
                  \  /         \  /                \  /           \  / 
mergeNumber=2   -1 -> 5       3 -> 4              0 -> 6        -2 -> 1
                    \         /                     \             /
mergeNumber=4    -1 -> 3 -> 4 -> 5               -2 -> 0 -> 1 -> 6
                            \                             /
mergeNumber=8            -2 -> -1 -> 0 -> 1 -> 3 -> 4 -> 5 -> 6
```



```java
    public ListNode sortList2(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        // 获取链表长度
        int len = getListLen(head);

        //归并数量 1 2 4 8，每次合并几个节点
        int mergeNumber = 1;

        while (mergeNumber < len) {
            ListNode pre = dummy;
            ListNode currentNode = dummy.next;
            // 找到合并链表的 h1 和 h2 头节点
            while (currentNode != null) {
                // 提取链表 l1 ， 个数为 mergeNumber
                int i = mergeNumber;
                ListNode l1 = currentNode;
                for (; currentNode != null && i > 0; i--) {
                    currentNode = currentNode.next;
                }
                // i > 0 说明没有链表 l2 直接返回,
                if (i > 0) break;

                // 提取链表 l2 ， 个数为 mergeNumber
                ListNode l2 = currentNode;
                i = mergeNumber;
                for (; currentNode != null && i > 0; i--) {
                    currentNode = currentNode.next;
                }

                // 求出两个链表的长度
                int l1Length = mergeNumber;
                // l2 长度可能 不足 mergeNumber
                int l2Length = mergeNumber - i;

                //比较两个有序链表 l1 l2，选择相对小的元素放入到合并空间(pre)，并移动链表指针到下一位置；
                while (l1Length > 0 && l2Length > 0) {
                    if (l1.val < l2.val) {
                        pre.next = l1;
                        l1 = l1.next;
                        l1Length--;
                    } else {
                        pre.next = l2;
                        l2 = l2.next;
                        l2Length--;
                    }
                    pre = pre.next;
                }
                //两个链表 合并完一个后 另外一个可能出现剩余，全部放入到合并空间(pre)
                pre.next = l1Length > 0 ? l1 : l2;
                // 并移动 pre 下次使用。
                while (l1Length > 0 || l2Length > 0) {
                    pre = pre.next;
                    l1Length--;
                    l2Length--;
                }
                //将未操作的数据放入到合并空间(pre)
                pre.next = currentNode;
            }
            mergeNumber *= 2;
        }
        return dummy.next;
    }

    private int getListLen(ListNode head) {
        ListNode cur = head;
        int len = 0;
        while (cur != null) {
            len++;
            cur = cur.next;
        }
        return len;
    }
```

时间复杂度 ：*O*(*n* log *n*) 

空间复杂度 ：*O*(1)  