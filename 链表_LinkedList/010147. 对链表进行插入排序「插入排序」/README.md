## 题目

对链表进行插入排序。

插入排序算法：

- 插入排序是迭代的，每次只移动一个元素，直到所有元素可以形成一个有序的输出列表。
- 每次迭代中，插入排序只从输入数据中移除一个待排序的元素，找到它在序列中适当的位置，并将其插入。
- 重复直到所有输入数据插入完为止。


**示例 1:**

```
输入: 4->2->1->3
输出: 1->2->3->4
```

**示例 2:**

```
输入: -1->5->3->4->0
输出: -1->0->3->4->5
```

## 解法一

`head` 为传入的链表头节点。

```java
ListNode dummy = new ListNode(0);
ListNode pre = dummy;
dummy.next = head;

dummy -> -1 -> 5 -> 3 -> 4 -> 0
  ↑       ↑
 pre     head
```

将链表分为已排序部分，和未排序部分。

```java
//初始化已排序部分：插入第一个点
//记录第二个节点
ListNode temp = head.next;
//将第一个节点与后面的节点断开
head.next = null;
// head 为未排序部分，pre 为已排序部分
head = temp;




dummy -> -1 
  ↑           
 pre  
  
 5 -> 3 -> 4 -> 0
 ↑
head
```

`temp` 用于临时记录 `head` 的后一个节点，是用于提取当前未排序的第一个节点的辅助指针。

开始循环 `head` 未排序链表。

```java
while (head != null) {
  //总是从头开始比较
  pre = dummy;
  //分割出剩余部分的第一个点，避免出现环。“断链避免循环”是链表的一个重点。
  temp = head.next;
  head.next = null;
  //...

  
  
dummy -> -1    5 
  ↑            ↑
 pre          head
  
 3 -> 4 -> 0
 ↑
temp
```

然后查看 `head` 节点应该插入到排序链表中的那个位置，插入后更新 `head`。

```java
  //...
  //查看当前 提取出来的 head 节点，应该存放在 pre 已排序链表的那个位置
  while (pre.next != null && head.val > pre.next.val) {
    pre = pre.next;
  }
  //插入节点
  head.next = pre.next;
  pre.next = head;

  //更新未排序链表，删除了第一个节点
  head = temp;
}

dummy -> -1 -> 5 
  ↑            
 pre          
  
 3 -> 4 -> 0
 ↑
head
```

然后依次循环插入。

完整代码：

```java
    public ListNode insertionSortList(ListNode head) {
        if (head == null) return null;

        ListNode dummy = new ListNode(0);
        ListNode pre = dummy;
        dummy.next = head;

        //初始化已排序部分：插入第一个点
        //记录第二个节点
        ListNode temp = head.next;
        //将第一个节点与后面的节点断开
        head.next = null;
        // head 为未排序部分，pre/dummy 为已排序部分
        head = temp;

        while (head != null) {
            //总是从头开始比较
            pre = dummy;
            //分割出剩余部分的第一个点，避免出现环。“断链避免循环”是链表的一个重点。
            temp = head.next;
            head.next = null;

            //查看当前 提取出来的 head 节点，应该存放在 pre 已排序链表的哪个位置
            while (pre.next != null && head.val > pre.next.val) {
                pre = pre.next;
            }
            //插入节点
            head.next = pre.next;
            pre.next = head;

            //更新未排序链表，删除了第一个节点
            head = temp;
        }

        return dummy.next;
    }
```

## 优化解法一

如果 未排序的头节点 > 已排序的尾结点，直接将 未排序的头节点 链接到 已排序 的最后面。

```java
    public ListNode insertionSortList2(ListNode head) {
        if (head == null || head.next == null)
            return head;

        ListNode dummy = new ListNode(Integer.MIN_VALUE);
        dummy.next = head;
        //未排序链头节点
        ListNode cur = head.next;
        //已排序的尾结点
        ListNode pre = head;
        while (cur != null) {
            //临时节点，用于更新 未排序链表头节点(head)
            ListNode temp = cur.next;
            //如果 未排序(cur) 链表的头节点 < 已排序(dummy) 链表的尾结点(pre) ，证明需要插入
            if (cur.val < pre.val) {
                ListNode start = dummy;
                //确定应该插入的位置
                while (cur.val > start.next.val) {
                    start = start.next;
                }
                pre.next = cur.next;
                cur.next = start.next;
                start.next = cur;

                cur = temp;
            } else {
                //否则 直接链接到 已排序(dummy) 链表的尾结点，减少特殊情况的比较
                cur = cur.next;
                pre = pre.next;
            }

        }
        return dummy.next;
    }
```

