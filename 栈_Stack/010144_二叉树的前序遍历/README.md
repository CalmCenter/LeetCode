给你二叉树的根节点 `root` ，返回它节点值的 **前序** 遍历。



**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/09/15/inorder_1.jpg)

```
输入：root = [1,null,2,3]
输出：[1,2,3]
```

**示例 2：**

```
输入：root = []
输出：[]
```

**示例 3：**

```
输入：root = [1]
输出：[1]
```

**示例 4：**

![img](https://assets.leetcode.com/uploads/2020/09/15/inorder_5.jpg)

```
输入：root = [1,2]
输出：[1,2]
```

**示例 5：**

![img](https://assets.leetcode.com/uploads/2020/09/15/inorder_4.jpg)

```
输入：root = [1,null,2]
输出：[1,2]
```

**提示：**

- 树中节点数目在范围 `[0, 100]` 内
- `-100 <= Node.val <= 100`

# 方法一 递归

二叉树的前序遍历：按照访问 根节点 —— 左子树 —— 右子树 的方式遍历这棵树，而在访问 左子树 或者 右子树 的时候，我们按照**同样的方式**遍历，直到遍历完整棵树。因此整个遍历过程天然具有递归的性质

```java
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<Integer>();
        preorder(root, res);
        return res;
    }

    public void preorder(TreeNode root, List<Integer> res) {
        if (root == null) {
            return;
        }
        res.add(root.val);
        preorder(root.left, res);
        preorder(root.right, res);
    }
```

复杂度分析

时间复杂度：O(n)，其中 n 是二叉树的节点数。每一个节点恰好被遍历一次。

空间复杂度：O(n)，为递归过程中栈的开销，平均情况下为 O(logn)，最坏情况下树呈现链状，为  O(n)。

# 方法二：栈迭代

递归隐式的维护了一个栈，而迭代是显示的使用栈，所以两种方式实现和细节都相同，与中序遍历的区别是 `res.add` 的调用时机。

```java
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }

        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode node = root;
        while (!stack.isEmpty() || node != null) {
            // 寻找左端的节点
            while (node != null) {
                res.add(node.val);
                stack.push(node);
                node = node.left;
            }
            node = stack.pop();
            // 寻找右端的节点
            node = node.right;
        }
        return res;
    }
```

复杂度分析

时间复杂度：O(n)，其中 n 是二叉树的节点数。每一个节点恰好被遍历一次。

空间复杂度：O(n)，为迭代过程中显式栈的开销，平均情况下为 O(logn)，最坏情况下树呈现链状，为 O(n)。

# 方法三：Morris 遍历

一种可以在线性时间内巧妙的方法，只占用常数空间来实现前序遍历，这种方法由 `J. H. Morris` 在 `1979` 年的论文「`Traversing Binary Trees Simply and Cheaply`」中首次提出，因此被称为 `Morris` 遍历。它能将非递归的遍历空间复杂度降为 *O*(1)。

- 如果 x 无左孩子，先将 x 的值加入答案数组，再访问 x 的右孩子，即 x = x.right。
- 如果 x 有左孩子，则找到 x 左子树上最右的节点（即左子树前序遍历的最后一个节点），我们记为 predecessor。根据 predecessor 的右孩子是否为空，进行如下操作。
  - 如果 predecessor 的右孩子为空，则将其右孩子指向 x，然后访问x 的左孩子，即 x = x.left。
  - 如果 predecessor 的右孩子不为空，则此时其右孩子指向 x，说明我们已经遍历完 x 的左子树，我们将 predecessor 的右孩子置空，将 x 的值加入答案数组，然后访问 x 的右孩子，即 x = x.right。

重点操作，`root` 节点的 **左节点** 的 **最右子节点** 的 `next` 指向 `root` ，与中序遍历的区别是 `res.add` 的调用时机。

```java
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if (root == null) {
            return res;
        }
        TreeNode predecessor = null;
        while (root != null) {
            if (root.left != null) {
                // predecessor 节点就是当前 root 节点向左走一步，然后一直向右走至无法走为止
                predecessor = root.left;
                while (predecessor.right != null && predecessor.right != root) {
                    predecessor = predecessor.right;
                }
                if (predecessor.right == null) {
                    res.add(root.val);
                    predecessor.right = root;
                    root = root.left;
                    continue;
                } else {
                    // right 为 root
                    predecessor.right = null;
                }
            } else {
                res.add(root.val);
            }
            root = root.right;
        }
        return res;
    }
```

复杂度分析

时间复杂度：O(n)，其中 nn 是二叉树的节点数。没有左子树的节点只被访问一次，有左子树的节点被访问两次。

空间复杂度：O(1)。只操作已经存在的指针（树的空闲指针），因此只需要常数的额外空间。

