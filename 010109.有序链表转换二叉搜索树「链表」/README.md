## 题目

给定一个单链表，其中的元素按升序排序，将其转换为高度平衡的二叉搜索树。

本题中，一个高度平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

**示例1:**

```
给定的有序链表： [-10, -3, 0, 5, 9],

一个可能的答案是：[0, -3, 9, -10, null, 5], 它可以表示下面这个高度平衡二叉搜索树：

      0
     / \
   -3   9
   /   /
 -10  5

```

## 解法一 分治

将给定的有序链表转换为二叉搜索树的第一步是确定根节点。由于我们需要构造出平衡的二叉树，因此比较直观的想法是让根节点左子树中的节点个数与右子树中的节点个数尽可能接近。这样一来，左右子树的高度也会非常接近，可以达到高度差绝对值不超过 1 的题目要求。

首先使用 快慢指针法 找到中位数，确定根节点

然后递归当前根节点左（右）侧链表，找到左（右）侧链表中的中位数，对应左（右）侧子树。

```java
    public ListNode getMedian(ListNode left, ListNode right) {
        ListNode fast = left;
        ListNode slow = left;
        while (fast != right && fast.next != right) {
            fast = fast.next;
            fast = fast.next;
            slow = slow.next;
        }
        return slow;
    }
```

根据代码 快慢指针法 `right` 第一次传入的 `null` ，如果数据是偶数(添加一个 `null` 就变为了奇数，如下所示)，中位数是中间偏右的那一位。
如果 `right` 有值，并且数据是偶数，则中位数是中间偏左的那一位

根据传入数据 `left = 1 right = null` ，结果如下，确定了顶部根节点。 

```
 1 2 3 4 5 6 (null)           4
       ↑
      mid
```

然后进入左子树构建，继续找左边的中位数 `left = 1 right = 4` 确定顶部根节点的左节点，但是这是还没有赋值。 

```
 1 2 3 4               4
   ↑                 
  mid              2
```

让后继续找左子树，只剩一个 `1` ，`1` 左右都没有节点，直接返回并赋值给 `2` 的 `left` 所以：

```
           4
          
         2
        /
       1
```

然后回溯到节点 `2` 时递归右子树 `left = 3 right = 4 ` 这样计算下来 `mid = 3` 左右都没有节点，返回并赋值给 `2` 的 `right` 所以：

```
           4
          
         2
        / \
       1   3
```

然后回溯到节点 `4` ， `2` 节点返回并赋值给 `4` 的左节点 ，然后递归右子树 `left = 5 right = null ` 这样计算下来 `mid = 6` 

```
           4
         / 
       2       6
     /   \
    1     3
```

然后继续找左子树，只剩 `5` 了，所以直接返回并赋值给 `6` 的左节点，并且回溯 ，`6` 的右子树也为 `null` ，继续回溯，`6` 节点返回并赋值给 `4` 的右节点

```
           4
         /   \
       2       6
     /   \    /
    1     3  5
```

代码如下：

```java
    public TreeNode sortedListToBST(ListNode head) {
        return buildTree(head, null);
    }

    public TreeNode buildTree(ListNode left, ListNode right) {
        if (left == right) {
            return null;
        }
        ListNode mid = getMedian(left, right);
        TreeNode root = new TreeNode(mid.val);
        root.left = buildTree(left, mid);
        root.right = buildTree(mid.next, right);
        return root;
    }

    public ListNode getMedian(ListNode left, ListNode right) {
        ListNode fast = left;
        ListNode slow = left;
        while (fast != right && fast.next != right) {
            fast = fast.next;
            fast = fast.next;
            slow = slow.next;
        }
        return slow;
    }
```

这种方式在分治的时候还可以拆链表，就是下面这样

原理和之前类似，之前使用的 `left right` 指针 进行快慢指针法 和 递归，下面的方法是截断链表进行 快慢指针 和 递归。

```java
    public TreeNode sortedListToBST(ListNode head) {
        //边界条件的判断
        if (head == null)
            return null;
        if (head.next == null)
            return new TreeNode(head.val);
        //这里通过快慢指针找到链表的中间结点 slow，pre 就是中间结点 slow 的前一个结点
        ListNode slow = head, fast = head, pre = null;
        while (fast != null && fast.next != null) {
            pre = slow;
            slow = slow.next;
            fast = fast.next.next;
        }
        //链表 断开为两部分，一部分是 node 的左子节点，一部分是 node 的右子节点
        pre.next = null;
        //node就是当前节点
        TreeNode node = new TreeNode(slow.val);
        //从 head 节点到 pre 节点是 node 左子树的节点
        node.left = sortedListToBST(head);
        //从 slow.next 到链表的末尾是 node 的右子树的结点
        node.right = sortedListToBST(slow.next);
        return node;
    }
```

复杂度分析

时间复杂度：O(nlogn)，其中 n 是链表的长度。

设长度为 n 的链表构造二叉搜索树的时间为 T(n)，递推式为 T(n)=2⋅T(n/2)+O(n)，根据主定理，T(n)=O(nlogn)。

空间复杂度：O(logn)，这里只计算除了返回答案之外的空间。平衡二叉树的高度为 O(logn)，即为递归过程中栈的最大深度，也就是需要的空间。

## 解法二 中序遍历

方法一的时间复杂度的瓶颈在于寻找中位数节点。

我们可以确定根节点位置，然后用 中序遍历 的思想填充数据，最后输出根节点。

首先拿到中位数的下标 `left = 0 , right = 5 , mid = (left + right + 1) / 2 = 3 `  ，然后进行树的创建，并没有填充数据

```
数据 4 5 6 7 8 9                    3( )
下标 0 1 2 3 4 5
           ↑
         3(mid)
```

首先遍历左子树， `left = 0 , right = 2 , mid = 1`

```
数据 4 5 6                         3( )
下标 0 1 2                     
       ↑                       1( )
     1(mid)
```

继续遍历左子树， `left = 0 , right = 1 , mid = 0` ，由于剩一个数据，所以开始按顺序填充数据，并回溯。

```
globalHead
 ↓
 4 5 6 7 8 9                       3( )
 0 1 2 3 4 5                    
 ↑                            1( )
0(mid)                       /
                         0(4)
```

回溯到上一层，并将节点 `4` 返回赋值给当前节点的左节点，按顺序填充当前节点 `5` 

```
globalHead
   ↓
 4 5 6 7 8 9                    3( )
 0 1 2 3 4 5         
   ↑                        1(5)
 1(mid)                     /
                         0(4)
```

然后遍历右子树，`left = 3 , right = 3 , min = 3` ，并且按顺序填充数据，由于只有一个数据，继续回溯到上一个节点，右子树创建完成，继续回溯到根节点

```
   globalHead
       ↓
 4 5 6 7 8 9                   3(7)
 0 1 2 3 4 5                   /
       ↑                     1(5)
    1(mid)                  /   \
                         0(4)   2(6)
```

然后遍历 `3` 的右子树，思想同上 

代码：

```java
    public TreeNode sortedListToBST(ListNode head) {
        globalHead = head;
        int length = getLength(head);
        return buildTree(0, length - 1);
    }

    public int getLength(ListNode head) {
        int ret = 0;
        while (head != null) {
            ++ret;
            head = head.next;
        }
        return ret;
    }

    public TreeNode buildTree(int left, int right) {
        if (left > right) {
            return null;
        }
        //根节点下标
        int mid = (left + right + 1) / 2;
        TreeNode root = new TreeNode();
        // 不断找左子树根节点，因为需要先填充左子树的数据
        root.left = buildTree(left, mid - 1);
        // 中序遍历
        root.val = globalHead.val;
        globalHead = globalHead.next;

        root.right = buildTree(mid + 1, right);
        return root;
    }
```

下面是按 `DFS` 思路 创建树 填充数据，思路类似，就是要 按照中序遍历的顺序 填充数据。

这里使用 `左子节点下标 是 根节点下标 的 两倍，右子节点下标 是 根节点下标 的 两倍加一` 的特性来找到各个节点，并使用中序遍历来顺序填充数据，下标必须从 `1` 开始。

```
               1
             /   \
            2      3
          /  \    /  \
         4    5  6    7
```

代码：

```java
    ListNode assignHead;

    public TreeNode sortedListToBST4(ListNode head) {
        assignHead = head;
        int len = 0;
        while (head != null) {
            len++;
            head = head.next;
        }
        TreeNode root = dfs(1, len);
        return root;
    }

    TreeNode dfs(int index, int n) {
        if (index > n) return null;
        TreeNode root = new TreeNode();
        // index * 2 表示当前节点的 左子节点
        root.left = dfs(index * 2, n);

        if (assignHead == null) return null;
        root.val = assignHead.val;
        assignHead = assignHead.next;
        // index * 2 + 1 表示当前节点的 右子节点
        root.right = dfs(index * 2 + 1, n);
        return root;
    }
```

