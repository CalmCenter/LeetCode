给你链表的头节点 `head` 和一个整数 `k` 。

**交换** 链表正数第 `k` 个节点和倒数第 `k` 个节点的值后，返回链表的头节点（链表 **从 1 开始索引**）。

**示例 1：**

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2021/01/10/linked1.jpg)

```
输入：head = [1,2,3,4,5], k = 2
输出：[1,4,3,2,5]
```

**示例 2：**

```
输入：head = [7,9,6,6,7,8,3,0,9,5], k = 5
输出：[7,9,6,6,8,7,3,0,9,5]
```

**示例 3：**

```
输入：head = [1], k = 1
输出：[1]
```

**示例 4：**

```
输入：head = [1,2], k = 1
输出：[2,1]
```

**示例 5：**

```
输入：head = [1,2,3], k = 2
输出：[1,2,3]
```

# 题解 双指针

`cur(c)  first(f) last(l)` `p` 表示第几个数字，用来确定 `k`

```
  k:2
p:1 2 3 4 5 6 7
  0 1 2 3 4 5 6 
  ↑
c/f/l

p:1 2 3 4 5 6 7
  0 1 2 3 4 5 6 
  ↑ ↑
  l c/f
  
p:1 2 3 4 5 6 7
  0 1 2 3 4 5 6 
    ↑ ↑
  l/f c
  
p:1 2 3 4 5 6 7
  0 1 2 3 4 5 6 
    ↑ ↑ ↑
    f l c
    
p:1 2 3 4 5 6 7
  0 1 2 3 4 5 6 
    ↑   ↑ ↑
    f   l c
    
p:1 2 3 4 5 6 7
  0 1 2 3 4 5 6 
    ↑     ↑ ↑
    f     l c
    
p:1 2 3 4 5 6 7
  0 1 2 3 4 5 6 
    ↑       ↑ ↑
    f       l c
    
p:1 2 3 4 5 6 7
  0 5 2 3 4 1 6 
```

```java
    public ListNode swapNodes(ListNode head, int k) {
        // 模拟指针，用来遍历链表
        ListNode cur = head;
        // 用来定位正数第k个节点
        ListNode first = head;
        // 用来定位倒数第k个节点
        ListNode last = head;
        // 用于节点的计数，和节点值的交换
        int count = 1;
        while (cur.next != null) {
            // 找到正数第k个节点
            if (count < k) {
                first = first.next;
                // 找到倒数第k个节点
            } else {
                last = last.next;
            }
            count++;
            cur = cur.next;
        }
        // 交换正数第k个节点和倒数第k个节点的值
        count = first.val;
        first.val = last.val;
        last.val = count;
        return head;
    }
```

