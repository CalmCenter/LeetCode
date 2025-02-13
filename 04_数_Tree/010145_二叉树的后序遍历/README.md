给定一个二叉树，返回它的 *后序* 遍历。

**示例:**

```
输入: [1,null,2,3]  
   1
    \
     2
    /
   3 

输出: [3,2,1]
```

# 方法一：递归

```java
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<Integer>();
        postorder(root, res);
        return res;
    }

    public void postorder(TreeNode root, List<Integer> res) {
        if (root == null) {
            return;
        }
        postorder(root.left, res);
        postorder(root.right, res);
        res.add(root.val);
    }
```

复杂度分析

时间复杂度：O(n)，其中 n 是二叉搜索树的节点数。每一个节点恰好被遍历一次。

空间复杂度：O(n)，为递归过程中栈的开销，平均情况下为 O(logn)，最坏情况下树呈现链状，为 O(n)。

# 方法二：迭代

```java
    public List<Integer> postorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<Integer>();
        if (root == null) {
            return res;
        }

        Deque<TreeNode> stack = new LinkedList<TreeNode>();
        TreeNode prev = null;
        while (root != null || !stack.isEmpty()) {
            // 寻找最左子树
            while (root != null) {
                stack.push(root);
                root = root.left;
            }

            root = stack.pop();
            // 如果有右节点 先找右子树
            // prev 的作用是记录上个添加的节点，如果是当前节点的右节点，则表明不需要在遍历右子树
            if (root.right == null || root.right == prev) {
                res.add(root.val);
                prev = root;
                root = null;
            } else {
                stack.push(root);
                root = root.right;
            }
        }
        return res;
    }
```

复杂度分析

时间复杂度：O(n)，其中 n 是二叉搜索树的节点数。每一个节点恰好被遍历一次。

空间复杂度：O(n)，为迭代过程中显式栈的开销，平均情况下为 O(logn)，最坏情况下树呈现链状，为 O(n)。

# 方法三：Morris 遍历

```java
        while (p1 != null) {
            if (p1.left != null) {
                // 查找左子树最右边的节点
                predecessor = p1.left;
                while (predecessor.right != null && predecessor.right != p1) {
                    predecessor = predecessor.right;
                }
                // 链接右父节点
                if (predecessor.right == null) {
                    predecessor.right = p1;
                    p1 = p1.left;
                    continue;
                } else {
                    // predecessor.right 指向 当前节点 p1
                    predecessor.right = null;
                    // 从底到顶添加 p1.left 以及 p1.left 每级的右节点
                    addPath(res, p1.left);
                }
            }
            // 返回第一个右父节点
            // 或者遍历右子树
            p1 = p1.right;
        }
        // 添加根节点
        addPath(res, root);
        return res;
    }
    // 添加右子树的右节点，从底到顶
    public void addPath(List<Integer> res, TreeNode node) {
        int count = 0;
        while (node != null) {
            ++count;
            res.add(node.val);
            node = node.right;
        }
        int left = res.size() - count, right = res.size() - 1;
        while (left < right) {
            int temp = res.get(left);
            res.set(left, res.get(right));
            res.set(right, temp);
            left++;
            right--;
        }
    }
```

复杂度分析

时间复杂度：O(n)，

空间复杂度：O(1)。