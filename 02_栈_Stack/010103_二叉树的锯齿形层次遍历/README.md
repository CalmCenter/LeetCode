

给定一个二叉树，返回其节点值的锯齿形层次遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。

例如：
给定二叉树 [3,9,20,null,null,15,7]

```
    3
   / \
  9  20
    /  \
   15   7
```

返回锯齿形层次遍历如下：

```
[
  [3],
  [20,9],
  [15,7]
]
```

# 方法一 BFS 广度优先遍历

```java
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        if (root == null) {
            return new ArrayList<List<Integer>>();
        }

        List<List<Integer>> results = new ArrayList<List<Integer>>();

        // 添加第一层元素，每一层需要跟一个 null
        LinkedList<TreeNode> node_queue = new LinkedList<TreeNode>();
        node_queue.addLast(root);
        node_queue.addLast(null);

        LinkedList<Integer> level_list = new LinkedList<Integer>();
        boolean is_order_left = true;

        while (node_queue.size() > 0) {
            TreeNode curr_node = node_queue.pollFirst();
            if (curr_node != null) {
                // 处理当前层的元素，并且按照 is_order_left 排序
                if (is_order_left)
                    // 从左到右
                    level_list.addLast(curr_node.val);
                else
                    // 从右到左
                    level_list.addFirst(curr_node.val);

                // 添加下一层 元素，从左到右添加
                if (curr_node.left != null)
                    node_queue.addLast(curr_node.left);
                if (curr_node.right != null)
                    node_queue.addLast(curr_node.right);

            } else {
                // 每一层完成，将排好序的元素添加到 results
                results.add(level_list);
                level_list = new LinkedList<Integer>();
                // 下一层已经添加的元素最后添加 null
                if (node_queue.size() > 0)
                    node_queue.addLast(null);
                // 变换方向
                is_order_left = !is_order_left;
            }
        }
        return results;
    }
```

复杂度分析

时间复杂度：O(N)，其中 N 是树中节点的数量。

空间复杂度：O(N)

# 方法二：DFS （深度优先遍历）

```java

    protected void DFS(TreeNode node, int level, List<List<Integer>> results) {
        // level 是当前层数 从 1 开始
        // results.size 表示添加了几层
        // 当前层数 > 已经添加了几层  证明需要添加新的层
        if (level > results.size()) {
            LinkedList<Integer> newLevel = new LinkedList<>();
            newLevel.add(node.val);
            results.add(newLevel);
        } else {
            // level 为偶数时
            if (level % 2 == 0)
                // 将当前的数据添加到该层最前面，当前层数据将从右往左
                results.get(level - 1).add(0, node.val);
            else
                // 从左往右
                results.get(level - 1).add(node.val);

        }

        if (node.left != null) DFS(node.left, level + 1, results);
        if (node.right != null) DFS(node.right, level + 1, results);
    }

    public List<List<Integer>> zigzagLevelOrder2(TreeNode root) {
        if (root == null) {
            return new ArrayList<>();
        }
        List<List<Integer>> results = new ArrayList<>();
        DFS(root, 1, results);
        return results;
    }

```

复杂度分析

时间复杂度：O(N)，其中 N 是树中节点的数量。

空间复杂度：O(N)

