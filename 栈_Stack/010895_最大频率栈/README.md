实现 `FreqStack`，模拟类似栈的数据结构的操作的一个类。

`FreqStack` 有两个函数：

- `push(int x)`，将整数 `x` 推入栈中。
- `pop()`，它**移除**并返回栈中出现最频繁的元素。
  - 如果最频繁的元素不只一个，则移除并返回最接近栈顶的元素。

**示例：**

```
输入：
["FreqStack","push","push","push","push","push","push","pop","pop","pop","pop"],
[[],[5],[7],[5],[7],[4],[5],[],[],[],[]]
输出：[null,null,null,null,null,null,null,5,7,5,4]
解释：
执行六次 .push 操作后，栈自底向上为 [5,7,5,7,4,5]。然后：

pop() -> 返回 5，因为 5 是出现频率最高的。
栈变成 [5,7,5,7,4]。

pop() -> 返回 7，因为 5 和 7 都是频率最高的，但 7 最接近栈顶。
栈变成 [5,7,5,4]。

pop() -> 返回 5 。
栈变成 [5,7,4]。

pop() -> 返回 4 。
栈变成 [5,7]。

```

**提示：**

- 对 `FreqStack.push(int x)` 的调用中 `0 <= x <= 10^9`。
- 如果栈的元素数目为零，则保证不会调用 `FreqStack.pop()`。
- 单个测试样例中，对 `FreqStack.push` 的总调用次数不会超过 `10000`。
- 单个测试样例中，对 `FreqStack.pop` 的总调用次数不会超过 `10000`。
- 所有测试样例中，对 `FreqStack.push` 和 `FreqStack.pop` 的总调用次数不会超过 `150000`。

#  题解 栈

```java
public class FreqStack {
    // 保存数值以及对于的出现次数
    Map<Integer, Integer> freq;
    // 出现次数以及对于的数值栈，那些数值出现过一次，那些数值出现过两次
    Map<Integer, Deque<Integer>> group;
    int maxfreq;

    public FreqStack() {
        freq = new HashMap();
        group = new HashMap();
        maxfreq = 0;
    }

    public void push(int x) {
        // 次数 + 1
        int f = freq.getOrDefault(x, 0) + 1;
        // 更新 x 对应的出现次数
        freq.put(x, f);
        // 记录最大出现次数
        if (f > maxfreq)
            maxfreq = f;
        // 将 x 保存在 出现 f 次的栈中
        group.computeIfAbsent(f, z-> new LinkedList<Integer>()).push(x);
    }

    public int pop() {
        // 取出最大次数的数值栈，取出栈顶的数值
        int x = group.get(maxfreq).pop();
        // 将数值对应的出现次数 -1
        freq.put(x, freq.get(x) - 1);
        // 查看 是否还存在 出现次数为 maxfreq 的值，没有则 -1
        if (group.get(maxfreq).size() == 0)
            maxfreq--;
        return x;
    }
}

        FreqStack obj = new FreqStack();
        obj.push(5);
        obj.push(7);
        obj.push(5);
        obj.push(7);
        obj.push(5);
        obj.push(4);
        System.out.println(obj.pop());
        System.out.println(obj.pop());
        System.out.println(obj.pop());
        System.out.println(obj.pop());
```

复杂度分析

时间复杂度：对于 push 和 pop 操作，O(1)。

空间复杂度：O(N)，其中 N 为 FreqStack 中元素的数目。

