## 题目

设计链表的实现。您可以选择使用单链表或双链表。单链表中的节点应该具有两个属性：`val` 和 `next`。`val` 是当前节点的值，`next` 是指向下一个节点的指针/引用。如果要使用双向链表，则还需要一个属性 `prev` 以指示链表中的上一个节点。假设链表中的所有节点都是 `0-index` 的。

在链表类中实现这些功能：

- `get(index)`：获取链表中第 `index` 个节点的值。如果索引无效，则返回 `-1`。
- `addAtHead(val)`：在链表的第一个元素之前添加一个值为 val 的节点。插入后，新节点将成为链表的第一个节点。
- `addAtTail(val)`：将值为 ` val` 的节点追加到链表的最后一个元素。
- `addAtIndex(index,val)` ：在链表中的第 `index` 个节点之前添加值为 `val`  的节点。如果 `index` 等于链表的长度，则该节点将附加到链表的末尾。如果 `index` 大于链表长度，则不会插入节点。如果 `index` 小于 `0` ，则在头部插入节点。
- `deleteAtIndex(index)` ：如果索引 `index`  有效，则删除链表中的第 `index` 个节点。

示例：

```
MyLinkedList linkedList = new MyLinkedList();
linkedList.addAtHead(1);
linkedList.addAtTail(3);
linkedList.addAtIndex(1,2);   //链表变为1-> 2-> 3
linkedList.get(1);            //返回2
linkedList.deleteAtIndex(1);  //现在链表是1-> 3
linkedList.get(1);            //返回3
```

**提示：**

- 所有`val`值都在 `[1, 1000]` 之内。
- 操作次数将在 `[1, 1000]` 之内。
- 请不要使用内置的 `LinkedList ` 库。

## 解法一 单链表

```java
    public class ListNode {
        int val;
        ListNode next;
        ListNode(int x) { val = x; }
    }

    class MyLinkedList {
        int size;
        ListNode head;  // 哨兵节点
        public MyLinkedList() {
            size = 0;
            head = new ListNode(0);
        }

        /** 获取链表中第 index 个节点的值。如果索引无效，则返回-1。 */
        public int get(int index) {
            // 如果索引是无效的
            if (index < 0 || index >= size) return -1;

            ListNode curr = head;

            for(int i = 0; i < index + 1; ++i) curr = curr.next;
            return curr.val;
        }

        /** 在链表的第一个元素之前添加一个值为 val 的节点。插入后，新节点将成为链表的第一个节点。 */
        public void addAtHead(int val) {
            addAtIndex(0, val);
        }

        /** 将值为 val 的节点追加到链表的最后一个元素。 */
        public void addAtTail(int val) {
            addAtIndex(size, val);
        }

        /** 在链表中的第 index 个节点之前添加值为 val  的节点。
         * 如果 index 等于链表的长度，则该节点将附加到链表的末尾。
         * 如果 index 大于链表长度，则不会插入节点。如果index小于0，则在头部插入节点。
         **/
        public void addAtIndex(int index, int val) {
            // 如果 index 大于链表长度，则不会插入节点。
            if (index > size) return;

            // 如果index小于0，则在头部插入节点。
            if (index < 0) index = 0;

            ++size;
            // 查找要添加的节点位置的前一个节点
            ListNode pred = head;
            for(int i = 0; i < index; ++i) pred = pred.next;

            // 创建节点
            ListNode toAdd = new ListNode(val);
            // 插入节点
            toAdd.next = pred.next;
            pred.next = toAdd;
        }

        /** 如果索引 index 有效，则删除链表中的第 index 个节点 */
        public void deleteAtIndex(int index) {
            // 如果索引无效，则不执行任何操作
            if (index < 0 || index >= size) return;

            size--;
            // 查找要添加的节点位置的前一个节点
            ListNode pred = head;
            for(int i = 0; i < index; ++i) pred = pred.next;

            // 删除
            pred.next = pred.next.next;
        }
    }
```

复杂度分析

时间复杂度：

- addAtHead： O(1)
- addAtInder，get，deleteAtIndex: O(k)，其中 k 指的是元素的索引。
- addAtTail：O(N)，其中 N 指的是链表的元素个数。

空间复杂度：所有的操作都是 O(1)。

## 解法二 双链表

```java
    public class ListNode {
        int val;
        ListNode next;
        ListNode prev;
        ListNode(int x) { val = x; }
    }

    class MyLinkedList {
        int size;
        // sentinel nodes as pseudo-head and pseudo-tail
        ListNode head, tail;
        public MyLinkedList() {
            size = 0;
            head = new ListNode(0);
            tail = new ListNode(0);
            head.next = tail;
            tail.prev = head;
        }

        /** 获取链表中第 index 个节点的值。如果索引无效，则返回-1。 */
        public int get(int index) {
            // 如果索引是无效的
            if (index < 0 || index >= size) return -1;

            // 选择最快的方式:从头部移动或从尾部移动
            ListNode curr = head;
            if (index + 1 < size - index)
                for(int i = 0; i < index + 1; ++i) curr = curr.next;
            else {
                curr = tail;
                for(int i = 0; i < size - index; ++i) curr = curr.prev;
            }

            return curr.val;
        }

        /** 在链表的第一个元素之前添加一个值为 val 的节点。插入后，新节点将成为链表的第一个节点。 */
        public void addAtHead(int val) {
            ListNode pred = head, succ = head.next;

            ++size;
            ListNode toAdd = new ListNode(val);
            toAdd.prev = pred;
            toAdd.next = succ;
            pred.next = toAdd;
            succ.prev = toAdd;
        }

        /** 将值为 val 的节点追加到链表的最后一个元素。 */
        public void addAtTail(int val) {
            ListNode succ = tail, pred = tail.prev;

            ++size;
            ListNode toAdd = new ListNode(val);
            toAdd.prev = pred;
            toAdd.next = succ;
            pred.next = toAdd;
            succ.prev = toAdd;
        }

        /** 在链表中的第 index 个节点之前添加值为 val  的节点。
         * 如果 index 等于链表的长度，则该节点将附加到链表的末尾。
         * 如果 index 大于链表长度，则不会插入节点。如果index小于0，则在头部插入节点。
         **/
        public void addAtIndex(int index, int val) {
            // 如果 index 大于链表长度，则不会插入节点。
            if (index > size) return;

            // 如果index小于0，则在头部插入节点。
            if (index < 0) index = 0;

            // 查找要添加的节点的前身和后继
            ListNode pred, succ;
            if (index < size - index) {
                pred = head;
                for(int i = 0; i < index; ++i) pred = pred.next;
                succ = pred.next;
            }
            else {
                succ = tail;
                for (int i = 0; i < size - index; ++i) succ = succ.prev;
                pred = succ.prev;
            }

            // 插入节点
            ++size;
            ListNode toAdd = new ListNode(val);
            toAdd.prev = pred;
            toAdd.next = succ;
            pred.next = toAdd;
            succ.prev = toAdd;
        }

        /** 如果索引 index 有效，则删除链表中的第 index 个节点. */
        public void deleteAtIndex(int index) {
            // 如果索引无效，则不执行任何操作
            if (index < 0 || index >= size) return;

            // 查找要删除的节点的前身和后续节点
            ListNode pred, succ;
            if (index < size - index) {
                pred = head;
                for(int i = 0; i < index; ++i) pred = pred.next;
                succ = pred.next.next;
            }
            else {
                succ = tail;
                for (int i = 0; i < size - index - 1; ++i) succ = succ.prev;
                pred = succ.prev.prev;
            }

            // 删除
            --size;
            pred.next = succ;
            succ.prev = pred;
        }
    }
```

复杂度分析

时间复杂度：

- addAtHead，addAtTail：O(1)
- get，addAtIndex，delete：O(min(k,N−k))，其中 k 指的是元素的索引。

空间复杂度：所有的操作都是 O(1)。