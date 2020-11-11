## 题目

请判断一个链表是否为回文链表。

示例 1:

```
输入: 1->2
输出: false
```

示例 2:

```
输入: 1->2->2->1
输出: true
```

进阶：
你能否用 O(n) 时间复杂度和 O(1) 空间复杂度解决此题？

## 方法一：递归

```
 1 -> 2 -> 3 -> 3 -> 2 -> 1
 ↑                        ↑
frontPointer            currentNode
```

`currentNode` 开始回溯往前走，`frontPointer` 每次回溯都会指向 `next` 。

```java
    private ListNode frontPointer;

    private boolean recursivelyCheck(ListNode currentNode) {
        if (currentNode != null) {
            // 从后往前 回溯 链表
            // 只要有一个返回 false ，全部返回 false。
            if (!recursivelyCheck(currentNode.next)) {
                return false;
            }
            if (currentNode.val != frontPointer.val) {
                return false;
            }
            frontPointer = frontPointer.next;
        }
        // 最后一个 null 节点不判断，直接返回 true
        return true;
    }

    public boolean isPalindrome(ListNode head) {
        frontPointer = head;
        return recursivelyCheck(head);
    }
```

**复杂度分析**

- 时间复杂度：*O*(*n*)，其中 *n* 指的是链表的大小。
- 空间复杂度：*O*(*n*)

## 方法二：快慢指针

找出中间点，反转后半部分，

```
head : 123321
```

反转后就是

 ```
           head : 123
secondHalfStart : 123
 ```

开始用 `head` 指针和 `secondHalfStart` 指针判断是否是回文。

如果数据时奇数 

```
head : 12321
```

根据快慢指针分割链表，然后反转后是这样的

```
           head : 123
secondHalfStart : 12
```

由于 `while` 判断 `result && p2 != null`  `p2` 是 `secondHalfStart` 的值，所以多出的一个节点不会判断（中间点），只判断其余节点是否相同。

```java
    public boolean isPalindrome(ListNode head) {
        if (head == null) {
            return true;
        }

        // 找到前半部分链表的尾节点并反转后半部分链表
        ListNode firstHalfEnd = endOfFirstHalf(head);
        ListNode secondHalfStart = reverseList(firstHalfEnd.next);

        // 判断是否回文
        ListNode p1 = head;
        ListNode p2 = secondHalfStart;
        boolean result = true;
        while (result && p2 != null) {
            if (p1.val != p2.val) {
                result = false;
            }
            p1 = p1.next;
            p2 = p2.next;
        }        

        // 还原链表并返回结果
        // firstHalfEnd.next = reverseList(secondHalfStart);
        return result;
    }

    private ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode nextTemp = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextTemp;
        }
        return prev;
    }

    private ListNode endOfFirstHalf(ListNode head) {
        ListNode fast = head;
        ListNode slow = head;
        while (fast.next != null && fast.next.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }

```

复杂度分析

时间复杂度：O(n)，其中 n 指的是链表的大小。

空间复杂度：O(1)。我们只会修改原本链表中节点的指向，而在堆栈上的堆栈帧不超过 O(1)。

