## 题目

给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。

要求返回这个链表的 深拷贝。 

## 解法一 递归

回溯过程中，需要防止随机指针形成环，导致死循环的问题，我们需要将已经拷贝过的节点保存起来，并将 `key` 为被拷贝的节点，如果 即将拷贝的节点 在 `HashMap` 中已经存在，则直接返回当前 `key` 所对应的 `value` 值，不再进行回溯。

```java
    // 散列映射持有旧节点作为 键和新节点 作为其值。
    HashMap<Node, Node> visitedHash = new HashMap<>();

    public Node copyRandomList(Node head) {

        if (head == null) {
            return null;
        }

        // 如果 map 中已经存在即将要拷贝的节点，则返回 value 值，不再进行回溯，防止死循环
        if (this.visitedHash.containsKey(head)) {
            return this.visitedHash.get(head);
        }

        // 创建一个新节点值一样旧的节点。(即复制节点)
        Node node = new Node(head.val);

        // 保存新节点,因为可能会有循环遍历过程中由于随机性的随机指针,重新指向当前节点,照成死循环.
        this.visitedHash.put(head, node);

        node.next = this.copyRandomList(head.next);
        node.random = this.copyRandomList(head.random);

        return node;
    }
```

复杂度分析

时间复杂度：O(N) ，其中 N 是链表中节点的数目。
空间复杂度：O(N) 。如果我们仔细分析，我们需要维护一个回溯的栈，同时也需要记录已经被深拷贝过的节点，也就是维护一个已访问字典。渐进时间复杂度为 O(N) 。

递归方法的循环实现

```java
    public Node copyRandomList(Node head) {
        if(head == null) return null;
        Node cur = head;
        Map<Node, Node> map = new HashMap<>();
        // 复制各节点，并建立 “原节点 -> 新节点” 的 Map 映射
        while(cur != null) {
            map.put(cur, new Node(cur.val));
            cur = cur.next;
        }
        cur = head;
        // 构建新链表的 next 和 random 指向
        while(cur != null) {
            map.get(cur).next = map.get(cur.next);
            map.get(cur).random = map.get(cur.random);
            cur = cur.next;
        }
        // 返回新链表的头节点
        return map.get(head);
    }

```

