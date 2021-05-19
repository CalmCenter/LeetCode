实现一个二叉搜索树迭代器。你将使用二叉搜索树的根节点初始化迭代器。

调用 `next()` 将返回二叉搜索树中的下一个最小的数。

**示例：**

**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/25/bst-tree.png)**

```java
BSTIterator iterator = new BSTIterator(root);
iterator.next();    // 返回 3
iterator.next();    // 返回 7
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 9
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 15
iterator.hasNext(); // 返回 true
iterator.next();    // 返回 20
iterator.hasNext(); // 返回 false
```

# 题解一 扁平化二叉搜索树

将二叉树中序遍历添加到集合中

```java
    ArrayList<Integer> nodesSorted;
    int index;

    public BSTIterator(TreeNode root) {

        this.nodesSorted = new ArrayList<Integer>();

        this.index = -1;

        this._inorder(root);
    }

    private void _inorder(TreeNode root) {
        if (root == null) {
            return;
        }

        this._inorder(root.left);
        this.nodesSorted.add(root.val);
        this._inorder(root.right);
    }


    public int next() {
        return this.nodesSorted.get(++this.index);
    }


    public boolean hasNext() {
        return this.index + 1 < this.nodesSorted.size();
    }
```

- 时间复杂度：构造迭代器花费的时间为 O(N)，问题陈述只要求我们分析两个函数的复杂性，但是在实现类时，还要注意初始化类对象所需的时间；在这种情况下，时间复杂度与二叉搜索树中的节点数成线性关系。
  - `next()`：O(1)
  - `hasNext()`：O(1)
- 空间复杂度：O(N)，由于我们创建了一个数组来包含二叉搜索树中的所有节点值，这不符合问题陈述中的要求，任一函数的最大空间复杂度应为 O(h)，其中 h 指的是树的高度，对于平衡的二叉搜索树，高度通常为 logN。


# 题解二 

在初始化时添加 `root` 节点的所有左节点，最左节点为栈顶节点，然后开始弹出栈顶节点，并查添加栈顶节点的右节点，以及右节点的所有左节点，栈弹出的顺序就是 `roor` 节点的左节点，`roor` 节点，`root` 节点右节点的左节点，`root` 节点的右节点。

```java
    Deque<TreeNode> stack;

    public BSTIterator2(TreeNode root) {
        this.stack = new LinkedList<>();
        this._leftmostInorder(root);
    }

    private void _leftmostInorder(TreeNode root) {
        // 添加 所有的 左节点
        while (root != null) {
            this.stack.push(root);
            root = root.left;
        }
    }

    public int next() {
        TreeNode topmostNode = this.stack.pop();
      	// 先添加右节点，再添加右节点的所有左节点，所以弹出顺序是先弹 右节点的所有左节点，再弹右节点
        if (topmostNode.right != null) {
            this._leftmostInorder(topmostNode.right);
        }

        // 输出当前节点 
        return topmostNode.val;
    }

    public boolean hasNext() {
        return this.stack.size() > 0;
    }
```

**复杂度分析**

- 时间复杂度：
  - `hasNext()`：若栈中还有元素，则返回 `true`，反之返回 `false`。所以这是一个 *O*(1) 的操作。
  - `next()`：包含了两个主要步骤。一个是从栈中弹出一个元素，它是下一个最小的元素。这是一个 *O*(1) 的操作。然而，随后我们要调用帮助函数 `_inorder_left `，它需要递归的，将左节点添加到栈上，是线性时间的操作，最坏的情况下为 O(N)。但是我们只对含有右节点的节点进行调用，它也不会总是处理 N 个节点。只有当我们有一个倾斜的树，才会有 N 个节点。因此该操作的平均时间复杂度仍然是 O(1)，符合问题中所要求的。
- 空间复杂度：O(h)，使用了一个栈来模拟递归。