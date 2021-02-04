请定义一个队列并实现函数 `max_value` 得到队列里的最大值，要求函数`max_value`、`push_back` 和 `pop_front` 的**均摊**时间复杂度都是 O(1)。

若队列为空，`pop_front` 和 `max_value` 需要返回 -1

**示例 1：**

```
输入: 
["MaxQueue","push_back","push_back","max_value","pop_front","max_value"]
[[],[1],[2],[],[],[]]
输出: [null,null,null,2,1,2]
```

**示例 2：**

```
输入: 
["MaxQueue","pop_front","max_value"]
[[],[],[]]
输出: [null,-1,-1]
```

**限制：**

- `1 <= push_back,pop_front,max_value的总操作数 <= 10000`
- `1 <= value <= 10^5`

# 题解一 暴力

数组实现队列，获取最大值时遍历数组

```java
class MaxQueue {
    int[] q = new int[20000];
    int begin = 0, end = 0;

    public MaxQueue() {

    }

    public int max_value() {
        int ans = -1;
        // 遍历
        for (int i = begin; i != end; ++i) {
            ans = Math.max(ans, q[i]);
        }
        return ans;
    }

    public void push_back(int value) {
        q[end++] = value;
    }

    public int pop_front() {
        // 判断是否为 空
        if (begin == end) {
            return -1;
        }
        // 输出 q[begin]  后 begin + 1
        return q[begin++];
    }

}
```

**复杂度分析**

- 时间复杂度：O(1)，（插入，删除），O(n)（求最大值）。插入与删除只需要普通的队列操作，为 O(1)，求最大值需要遍历当前的整个队列，最坏情况下为 O(n)。

# 题解二 单调的双端队列

当一个元素进入队列的时候，它前面所有比它小的元素就不会再对答案产生影响。

举个例子，如果我们向队列中插入数字序列 `1 1 1 1 2` ，那么在第一个数字 `2` 被插入后，数字 `2` 前面的所有数字 `1` 将不会对结果产生影响。因为按照队列的取出顺序，数字 `2` 只能在所有的数字 `1` 被取出之后才能被取出，因此如果数字 `1` 如果在队列中，那么数字 `2` 必然也在队列中，最大值始终是 `2` ，使得数字 `1` 对结果没有影响。

```java
    // 保存队列的数据
    Queue<Integer> queue;
    // 保存队列每个阶段的最大值
    Deque<Integer> de;

    public MaxQueue2() {
        queue = new LinkedList<>();
        de = new LinkedList<>();
    }

    public int max_value() {
        if (de.isEmpty()) {
            return -1;
        }
        return de.peekFirst();
    }

    public void push_back(int value) {
        // 当一个元素进入队列的时候，它前面所有比它小的元素就不会再对答案产生影响。
        while (!de.isEmpty() && de.peekLast() < value) {
            de.pollLast();
        }
        de.offerLast(value);
        queue.offer(value);
    }

    public int pop_front() {
        if (queue.isEmpty()) {
            return -1;
        }
        int ans = queue.poll();
        // 如果是本阶段最大值本身则出队列，下一个阶段的最大值顶上！
        if (ans == de.peekFirst()) {
            de.pollFirst();
        }
        return ans;
    }
```

**复杂度分析**

- 时间复杂度：O(1)（插入，删除，求最大值）

  删除操作于求最大值操作显然只需要 O(1) 的时间。

  而插入操作虽然看起来有循环，做一个插入操作时最多可能会有 n 次出队操作。但要注意，由于每个数字只会出队一次，因此对于所有的 n 个数字的插入过程，对应的所有出队操作也不会大于 n 次。因此将出队的时间均摊到每个插入操作上，时间复杂度为 O(1)。

- 空间复杂度：O(n)，需要用队列存储所有插入的元素

