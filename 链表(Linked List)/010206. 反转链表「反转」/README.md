## 题目

反转一个单链表。

**示例:**

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

## 方法一：迭代

首先确定各个指针的位置 `ListNode nextTemp = curr.next;`

```
null   1 -> 2 -> 3 -> 4 -> 5
 ↑     ↑    ↑
prev  curr nextTemp   
```

然后开始换位置 `curr.next = prev;`

```
null <- 1     2 -> 3 -> 4 -> 5
 ↑      ↑     ↑
prev   curr nextTemp   
```

重新分配指针 `prev = curr;  curr = nextTemp;`

```
             curr
              ↓
null <- 1     2 -> 3 -> 4 -> 5
        ↑     ↑
      prev  nextTemp  
```

重复上面的操作。

```java
    public ListNode reverseList(ListNode head) {
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
```

**复杂度分析**

- 时间复杂度：*O*(*n*)，假设 *n* 是列表的长度，时间复杂度是 *O*(*n*)。
- 空间复杂度：*O*(1)。

## 方法二：递归

由于是递归，所以得从最后一个节点分析，又因为 `head == null || head.next == null` 返回了 `head` 所以最后一个节点也不做处理，而是直接返回到头部，也就是这里的 `p` ，所以 `head` 参数应该从倒数第二个节点开始分析

```
 1 -> 2 -> 3 -> 4 -> 5
                ↑    ↑
               head  p   
```

` head.next.next = head;  head.next = null;`  然后回溯上一层。

```
 1 -> 2 -> 3 -> 4 <- 5
           ↑         ↑
          head       p  
```

重复上面的操作。

```java
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode p = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return p;
    }
```

复杂度分析

时间复杂度：O(n)，假设 n 是列表的长度，那么时间复杂度为 O(n)。
空间复杂度：O(n)，由于使用递归，将会使用隐式栈空间。递归深度可能会达到 n 层。

## 方法三 迭代双指针优化

```
  head
   ↓
   1 -> 2 -> 3 -> 4 -> 5
   ↑         ↑
  cur       temp   
```

开始交换方向 `head.next.next = cur;` 

```
  head
   ↓
   1 <- 2    3 -> 4 -> 5
   ↑         ↑
  cur       temp   
```

`cur = head.next;  head.next = temp;`

```
   → → → → → →
   ↑         ↓
   1 <- 2    3 -> 4 -> 5
   ↑    ↑    ↑
 head  cur  temp   
```

上面是第一次循环

```
    → → → → → → → → 
   ↑              ↓
   1 <- 2 <- 3    4 -> 5
   ↑         ↑    ↑
 head       cur  temp   
```



```java
    public ListNode reverseList(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode cur = head;
        while (head.next != null) {
            ListNode temp = head.next.next;
            head.next.next = cur;
            cur = head.next;
            head.next = temp;
        }
        return cur;
    }
```

**复杂度分析**

- 时间复杂度：*O*(*n*)，假设 *n* 是列表的长度，时间复杂度是 *O*(*n*)。
- 空间复杂度：*O*(1)。