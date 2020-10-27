## 题目

多级双向链表中，除了指向下一个节点和前一个节点指针之外，它还有一个子链表指针，可能指向单独的双向链表。这些子列表也可能会有一个或多个自己的子项，依此类推，生成多级数据结构，如下面的示例所示。

给你位于列表第一级的头节点，请你扁平化列表，使所有结点出现在单级双链表中。

示例 1:

```
输入：head = [1,2,3,4,5,6,null,null,null,7,8,9,10,null,null,11,12]
输出：[1,2,3,7,8,11,12,9,10,4,5,6]

 1---2---3---4---5---6--NULL
         |
         7---8---9---10--NULL
             |
             11--12--NULL

```

示例 2:

```
输入：head = [1,2,null,3]
输出：[1,3,2]
解释：

输入的多级列表如下图所示：

  1---2---NULL
  |
  3---NULL

```

如何表示测试用例中的多级链表？

以 示例 1 为例：

```
 1---2---3---4---5---6--NULL
         |
         7---8---9---10--NULL
             |
             11--12--NULL
```

序列化其中的每一级之后：

```
[1,2,3,4,5,6,null]
[7,8,9,10,null]
[11,12,null]
```


为了将每一级都序列化到一起，我们需要每一级中添加值为 null 的元素，以表示没有节点连接到上一级的上级节点。

```
[1,2,3,4,5,6,null]
[null,null,7,8,9,10,null]
[null,11,12,null]
```

合并所有序列化结果，并去除末尾的 null 。

```
[1,2,3,4,5,6,null,null,null,7,8,9,10,null,null,11,12]
```


提示：

- 节点数目不超过 1000
- 1 <= `Node.val` <= 10^5

## 题解：

我们可能会疑问什么情况下会使用这样的数据结构。其中一个场景就是 `git` 分支的简化版本。通过扁平化多级列表，可以认为将所有 `git` 的分支合并在一起。

将示例一顺时针转 45 °，那么就会看到一颗二叉树，则扁平化的操作也就是对二叉树进行先序遍历（深度优先搜索）

```
                      1
                        \
                          2
                            \
                              3
                            /   \
                          7       4
                            \       \
                              8       5
                            /   \       \
                          11      9       6
                            \       \
                              12      10
```

如上图所示，我们可以将 `child` 指针当作二叉树中指向左子树的 `left` 指针。同样，`next` 指针可以当作是二叉树中的 `right` 指针。然后我们深度优先搜索树将得到答案。

### 递归

递归的深度优先搜索算法如下：

- 首先，我们定义递归函数 `flatten_dfs(prev, curr)`，它接收两个指针作为函数参数并返回扁平化列表中的尾部指针。`curr` 指针指向我们要扁平化的子列表，`prev` 指针指向 `curr` 指向元素的前一个元素。
- 在函数 `flatten_dfs(prev, curr)`，我们首先在 `prev` 和 `curr` 节点之间建立双向连接。
- 然后在函数中调用 `flatten_dfs(curr, curr.child)` 对左子树（`curr.child` 即子列表）进行操作，它将返回扁平化子列表的尾部元素 `tail`，再调用 `flatten_dfs(tail, curr.next) ` 对右子树进行操作。
- 为了得到正确的结果，我们还需要注意两个重要的细节：
  - 在调用 `flatten_dfs(curr, curr.child)` 之前我们应该复制 `curr.next` 指针，因为 `curr.next` 可能在函数中改变。
  - 在扁平化 `curr.child` 指针所指向的列表以后，我们应该删除 `child ` 指针，因为我们最终不再需要该指针。

```java
    public Node flatten(Node head) {
        if (head == null) return head;
        Node pseudoHead = new Node(0, null, head, null);

        flattenDFS(pseudoHead, head);

        pseudoHead.next.prev = null;
        return pseudoHead.next;
    }

    public Node flattenDFS(Node prev, Node curr) {
        if (curr == null) return prev;
        curr.prev = prev;
        prev.next = curr;
        // 记录当前 node 的 next 节点，回溯时使用
        Node tempNext = curr.next;
        // 优先遍历 child (相当于左子树)
        Node tail = flattenDFS(curr, curr.child);
        curr.child = null;
        // 遍历 next (相当于右子树)，会返回尾部节点到 tail。
        return flattenDFS(tail, tempNext);
    }
```

复杂度分析

时间复杂度：O(N)。N 指的是列表的节点数，深度优先搜索遍历每个节点一次。
空间复杂度：O(N)，N 指的是列表的节点数，二叉树很可能不是个平衡的二叉树，若节点仅通过 child 指针相互链接，则在递归调用的过程中堆栈的深度会达到 N。

### 迭代

 `stack` 数据结构，元素遵循后进先出的原则。

算法：

- 首先我们创建 `stack`，然后将头节点压栈。利用 `prev` 变量帮助我们记录在每个迭代过程的前继节点。
- 然后我们进入循环迭代 `stack` 中的元素，直到栈为空。
- 在每一次迭代过程中，首先在 `stack` 弹出一个节点（叫做 `curr`）。再建立 `prev` 和 `curr` 之间的双向链接，再顺序处理 `curr.next` 和 `curr.child` 指针所指向的节点，严格按照此顺序执行。
- 如果 `curr.next` 存在（即存在右子树），那么我们将 `curr.next` 压栈后进行下一次迭代。
- 如果 `curr.child` 存在（即存在左子树），那么将 `curr.child` 压栈，与 `curr.next` 不同的是，我们需要删除 `curr.child` 指针，因为在最终的结果不再需要使用它。

```
 1---2---3---4---5---6--NULL                      
         |
         7---8---9---10--NULL
             |
             11--12--NULL
             
stack : 1
        ↑
 		   push
 		   
 		   pop
        ↑
stack:  1   2
            ↑
 		      push

currList: 1

 		   pop
        ↑
stack:  2   3
            ↑
 		      push

currList: 1 -- 2
```

前三次循环如上所示，正常进栈出栈，按出栈顺序链接每个节点。当第三个节点出栈时，先添加 `next` 节点，再添加 `child` 节点。

```
 		   pop
        ↑
stack:  3   4   7
            ↑   ↑
 		      push push

currList: 1 -- 2 -- 3
```

根据 后进先出 的规则，当前 `pop` 为 `7` 节点，并且入栈 `7` 的 `next` 节点。然后继续循环

```
 		       pop
            ↑
stack:  4   7   8
                ↑
 		          push

currList: 1 -- 2 -- 3 -- 7

```

```
 		       pop
            ↑
stack:  4   8   9   11
                ↑    ↑
 		          push push
 		          
currList: 1 -- 2 -- 3 -- 7 -- 8
```

```
 		           pop
                ↑
stack:  4   9   11   12
                      ↑
 		                push
 		                
currList: 1 -- 2 -- 3 -- 7 -- 8 -- 11
```

```
 		           pop
                ↑
stack:  4   9   12   
                      
currList: 1 -- 2 -- 3 -- 7 -- 8 -- 11 -- 12   
```

```
 		      pop
            ↑
stack:  4   9   10
                 ↑
 		           push
 		           
currList: 1 -- 2 -- 3 -- 7 -- 8 -- 11 -- 12 -- 9
```

```
 		      pop
            ↑
stack:  4   10

 		           
currList: 1 -- 2 -- 3 -- 7 -- 8 -- 11 -- 12 -- 9 -- 10
```

```
 		   pop
        ↑
stack:  4   5
            ↑
 		      push
 		           
currList: 1 -- 2 -- 3 -- 7 -- 8 -- 11 -- 12 -- 9 -- 10 -- 4 
```

```
 		   pop
        ↑
stack:  5   6
            ↑
 		      push
 		           
currList: 1 -- 2 -- 3 -- 7 -- 8 -- 11 -- 12 -- 9 -- 10 -- 4 -- 5
```

最后

```
 		   pop
        ↑
stack:  6  
 		           
currList: 1 -- 2 -- 3 -- 7 -- 8 -- 11 -- 12 -- 9 -- 10 -- 4 -- 5 -- 6
```

代码：

```java
    public Node flatten(Node head) {
        if (head == null) return head;

        Node pseudoHead = new Node(0, null, head, null);
        Node curr, prev = pseudoHead;

        Deque<Node> stack = new ArrayDeque<>();
        stack.push(head);

        while (!stack.isEmpty()) {
            // 出栈一个节点，后进先出，
            // 如果之前添加过 child 节点，应该弹出 child 节点，开始处理 child 链表。
            curr = stack.pop();
            prev.next = curr;
            curr.prev = prev;

            // 入栈 next 节点
            if (curr.next != null) stack.push(curr.next);
            if (curr.child != null) {
                // 入栈 child 节点
                stack.push(curr.child);
                // don't forget to remove all child pointers.
                curr.child = null;
            }
            prev = curr;
        }
        // detach the pseudo node from the result
        pseudoHead.next.prev = null;
        return pseudoHead.next;
    }
```

**复杂度分析**

- 时间复杂度：O(*N*)。
- 空间复杂度：O(*N*)。