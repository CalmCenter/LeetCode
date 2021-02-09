请你设计一个队列，支持在前，中，后三个位置的 `push` 和 `pop` 操作。

请你完成 `FrontMiddleBack` 类：

- `FrontMiddleBack()` 初始化队列。
- `void pushFront(int val)` 将 `val` 添加到队列的 **最前面** 。
- `void pushMiddle(int val)` 将 `val` 添加到队列的 **正中间** 。
- `void pushBack(int val)` 将 `val` 添加到队里的 **最后面** 。
- `int popFront()` 将 **最前面** 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 `-1` 。
- `int popMiddle()` 将 **正中间** 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 `-1` 。
- `int popBack()` 将 **最后面** 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 `-1` 。

请注意当有 **两个** 中间位置的时候，选择靠前面的位置进行操作。比方说：

- 将 `6` 添加到 `[1, 2, 3, 4, 5]` 的中间位置，结果数组为 `[1, 2, **6**, 3, 4, 5]` 。
- 从 `[1, 2, **3**, 4, 5, 6]` 的中间位置弹出元素，返回 `3` ，数组变为 `[1, 2, 4, 5, 6]` 。

**示例 1：**

```
输入：
["FrontMiddleBackQueue", "pushFront", "pushBack", "pushMiddle", "pushMiddle", "popFront", "popMiddle", "popMiddle", "popBack", "popFront"]
[[], [1], [2], [3], [4], [], [], [], [], []]
输出：
[null, null, null, null, null, 1, 3, 4, 2, -1]
解释：
FrontMiddleBackQueue q = new FrontMiddleBackQueue();
q.pushFront(1);   // [1]
q.pushBack(2);    // [1, 2]
q.pushMiddle(3);  // [1, 3, 2]
q.pushMiddle(4);  // [1, 4, 3, 2]
q.popFront();     // 返回 1 -> [4, 3, 2]
q.popMiddle();    // 返回 3 -> [4, 2]
q.popMiddle();    // 返回 4 -> [2]
q.popBack();      // 返回 2 -> []
q.popFront();     // 返回 -1 -> [] （队列为空）
```

# 题解 

```java
public class FrontMiddleBackQueue {
    LinkedList<Integer> FBMqueue;

    public FrontMiddleBackQueue() {
        this.FBMqueue = new LinkedList<Integer>();
    }

    // 将 val 添加到队列的 最前面
    public void pushFront(int val) {
        FBMqueue.addFirst(val);
    }

    // 将 val 添加到队列的 正中间 。
    public void pushMiddle(int val) {
        int index = FBMqueue.size() / 2;
        FBMqueue.add(index, val);
    }

    // 将 val 添加到队里的 最后面
    public void pushBack(int val) {
        FBMqueue.addLast(val);
    }

    //将 最前面 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 -1
    public int popFront() {
        if (FBMqueue.isEmpty()) return -1;
        return FBMqueue.removeFirst();
    }

    // 将 正中间 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 -1
    public int popMiddle() {
        int index = FBMqueue.size() / 2;
        if (FBMqueue.isEmpty()) return -1;
        if (FBMqueue.size() % 2 == 0) return FBMqueue.remove(index - 1);
        return FBMqueue.remove(index);
    }

    // 将 最后面 的元素从队列中删除并返回值，如果删除之前队列为空，那么返回 -1
    public int popBack() {
        if (FBMqueue.isEmpty()) return -1;
        return FBMqueue.removeLast();
    }

}

```

# 题解二 

```java

class ListNode{
    int val;
    ListNode last;
    ListNode next;
    public ListNode(){};
    public ListNode(int val){this.val = val;};
}
class FrontMiddleBackQueue {

    ListNode top; // 首部的哨兵节点
    ListNode tail; // 尾部的哨兵节点
    ListNode mid; // 用于记录中间节点位置的指针
    int length; // 记录双向链表长度

    public FrontMiddleBackQueue() {
        // 创建首尾的哨兵节点，并连接在一起
        top = new ListNode(0);
        tail = new ListNode(0);
        top.next = tail;
        tail.last = top;

        length = 0;
    }

    public void pushFront(int val) {
        ListNode node = new ListNode(val);
        // 先该节点的 next 指向双向链变的第一个节点（即top.next）
        // 再双向链变的第一个节点（top.next）的last指向该节点
        node.next = top.next;
        top.next.last = node;
        // 该节点与首节点连接在一起
        node.last = top;
        top.next = node;

        length++;

        // 修改mid指针的位置
        if(length == 1){
            // 说明时第一个节点，那么mid就是它
            mid = node;
        }else if(length % 2 == 0){
            // 那么mid往前移一位
            mid = mid.last;
        }
        // System.out.println("pushFront:" + length);
    }

    public void pushMiddle(int val) {
        if(length == 0){
            pushFront(val);
        }else{
            ListNode node = new ListNode(val);

            if(length % 2 == 0){
                // 偶数放mid后面
                node.next = mid.next;
                mid.next.last = node;
                mid.next = node;
                node.last = mid;
            }else{
                // 奇数放mid前面
                node.last = mid.last;
                mid.last.next = node;
                node.next = mid;
                mid.last = node;
            }


            length++;

            // 修改mid指针位置
            if(length % 2 == 0){
                mid = mid.last;
            }else{
                mid = mid.next;
            }
            // System.out.println("pushMiddle:" + length);
        }
    }

    public void pushBack(int val) {
        ListNode node = new ListNode(val);
        // 该节点的last先指向双向链表的最后一个节点（即tail.last）
        // 双向链表的最后一个节点（tail.last）的next再指向该节点
        node.last = tail.last;
        tail.last.next = node;
        // 该节点再与尾节点连接在一起
        node.next = tail;
        tail.last = node;

        length++;

        // 修改mid的位置
        if(length == 1){
            mid = node;
        }else if(length % 2 != 0){
            // 那么mid往后移一位
            mid = mid.next;
        }
        // System.out.println("pushBack:" + length);
    }

    public int popFront() {
        int ret = -1;
        if(length != 0){
            // 当双向链表不为空时
            // 删除双向链表的第一个节点，只要让首节点与第二个节点连接在一起即可
            ret = top.next.val; // 记录返回值
            top.next.next.last = top;
            top.next = top.next.next;

            length--;

            // 修改mid指针位置
            if(length == 0){
                mid = null;
            }else if(length % 2 != 0){
                mid = mid.next;
            }
            // System.out.println("popFront:" + length);
        }

        return ret;
    }

    public int popMiddle() {
        int ret = -1;
        if(length != 0){
            ret = mid.val;
            mid.next.last = mid.last;
            mid.last.next = mid.next;

            length--;

            // 修改mid指针位置
            if(length % 2 == 0){
                mid = mid.last;
            }else{
                mid = mid.next;
            }
            // System.out.println("popMiddle:" + length);
        }

        return ret;
    }

    public int popBack() {
        int ret = -1;
        if(length != 0){
            // 当双向链表不为空时
            // 删除双向链表的最后一个节点，只需要让尾节点与倒数第二个节点连接在一起即可
            ret = tail.last.val;
            tail.last.last.next = tail;
            tail.last = tail.last.last;

            length--;

            // 修改mid指针位置
            if(length == 0){
                mid = null;
            }else if(length % 2 == 0){
                mid = mid.last;
            }
            // System.out.println("popBack:" + length);
        }

        return ret;
    }
}
```

