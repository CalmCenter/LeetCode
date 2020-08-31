## 题目

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

**示例1:**

```
给定 1->2->3->4, 你应该返回 2->1->4->3.
```

## 解题

### 方法  1 ：递归

- 从链表的头节点 `head` 开始递归，两两分组每个节点
- 每次递归都负责交换一组节点。由 `firstNode（奇数位）` 和 `secondNode（偶数位）` 表示要交换的两个节点。
- 下一次递归则是传递的是下一组需要交换的节点（奇数位节点）。若链表中还有节点，则继续递归。
- 交换了两个节点以后，返回 `secondNode`，因为它是交换后组内的新头。
- 在所有节点交换完成以后，我们返回交换后的头，实际上是原始链表的第二个节点。

```java
    public ListNode swapPairs(ListNode head) {
        //head 表示每一组奇数位
        if ((head == null) || (head.next == null)) {
            return head;
        }
        //保存奇数位
        ListNode firstNode = head;
        //保存偶数位
        ListNode secondNode = head.next;

        //两两分组，将当前 奇数位 next 指向下一组返回的头结点（由后往前处理，所以这里返回的已经是下一组的偶数位）
        firstNode.next = swapPairs(secondNode.next);
        //将当前组 偶数位  next 指向当前组 奇数位（组内交换）
        secondNode.next = firstNode;
        //返回偶数位，因为 偶数位 是交换后组内的新头结点
        return secondNode;
    }
```

**复杂度分析**

- 时间复杂度：*O*(*N*)，其中 *N* 指的是链表的节点数量。
- 空间复杂度：*O*(*N*)，递归过程使用的堆栈空间。

### 方法二：迭代

同样是两两分组，但是这是从前往后操作节点

- `firstNode` 和 `secondNode` 分别表示奇数节点和偶数节点，即两步看作一步。

- ```java
   firstNode.next = secondNode.next
   secondNode.next = firstNode
  ```

  交换两个节点

- `prevNode.next = secondNode` 操作的 `dummy` 确定每组的头节点

```java
    public ListNode swapPairs(ListNode head) {
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode prevNode = dummy;
        while ((head != null) && (head.next != null)) {
            //两两分组,head 表示每一组奇数位节点
            //奇数位节点
            ListNode firstNode = head;
            //偶数位节点
            ListNode secondNode = head.next;

            //换位置
            firstNode.next = secondNode.next;
            secondNode.next = firstNode;
          
            // prevNode 一般为上一组的 后一个节点（除了第一次为 -1 ）
            // 上一组的 后一个节点 指向当前组 的 头节点（ secondNode 经过交换成为了当前组头的节点）
            prevNode.next = secondNode;//操作的 dummy
          
            //经过交换 firstNode 成为了 本组后一个节点
            //更新 prevNode 为 本组 后一个节点
            prevNode = firstNode;
            //赋值为 下一组 奇数位数据
            head = firstNode.next;
        }
        return dummy.next;
    }
```

**复杂度分析**

- 时间复杂度：*O*(*N*)，其中 *N* 指的是链表的节点数量。
- 空间复杂度：*O*(1)。