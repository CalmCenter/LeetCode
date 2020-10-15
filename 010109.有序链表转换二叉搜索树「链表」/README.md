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

这种方式简单写，就是下面这样

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

我们可以确定根节点位置，然后用 中序遍历 的思想填充数据，最后输出根节点就可以。

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

下面是按 `DFS` 思路创建树填充数据，思路类似，就是要 按照中序遍历的顺序 填充数据。

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

