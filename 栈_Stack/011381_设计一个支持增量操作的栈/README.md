请你设计一个支持下述操作的栈。

实现自定义栈类 `CustomStack` ：

- `CustomStack(int maxSize)`：用 `maxSize` 初始化对象，`maxSize` 是栈中最多能容纳的元素数量，栈在增长到 `maxSize` 之后则不支持 `push` 操作。
- `void push(int x)`：如果栈还未增长到 `maxSize` ，就将 `x` 添加到栈顶。
- `int pop()`：弹出栈顶元素，并返回栈顶的值，或栈为空时返回 **-1** 。
- `void inc(int k, int val)`：栈底的 `k` 个元素的值都增加 `val` 。如果栈中元素总数小于 `k` ，则栈中的所有元素都增加 `val` 。

**示例：**

```java
输入：
["CustomStack","push","push","pop","push","push","push","increment","increment","pop","pop","pop","pop"]
[[3],[1],[2],[],[2],[3],[4],[5,100],[2,100],[],[],[],[]]
输出：
[null,null,null,2,null,null,null,null,null,103,202,201,-1]
解释：
CustomStack customStack = new CustomStack(3); // 栈是空的 []
customStack.push(1);                          // 栈变为 [1]
customStack.push(2);                          // 栈变为 [1, 2]
customStack.pop();                            // 返回 2 --> 返回栈顶值 2，栈变为 [1]
customStack.push(2);                          // 栈变为 [1, 2]
customStack.push(3);                          // 栈变为 [1, 2, 3]
customStack.push(4);                          // 栈仍然是 [1, 2, 3]，不能添加其他元素使栈大小变为 4
customStack.increment(5, 100);                // 栈变为 [101, 102, 103]
customStack.increment(2, 100);                // 栈变为 [201, 202, 103]
customStack.pop();                            // 返回 103 --> 返回栈顶值 103，栈变为 [201, 202]
customStack.pop();                            // 返回 202 --> 返回栈顶值 202，栈变为 [201]
customStack.pop();                            // 返回 201 --> 返回栈顶值 201，栈变为 []
customStack.pop();                            // 返回 -1 --> 栈为空，返回 -1

```

# 题解一

使用数组模拟栈，用一个变量 `top` 来记录当前栈顶的位置。

```java
    int[] stack;
    int top;

    public CustomStack(int maxSize) {
        stack = new int[maxSize];
        top = -1;
    }
    
    public void push(int x) {
        if (top != stack.length - 1) {
            ++top;
            stack[top] = x;
        }
    }
    
    public int pop() {
        if (top == -1) {
            return -1;
        }
        --top;
        return stack[top + 1];
    }
    
    public void increment(int k, int val) {
        int limit = Math.min(k, top + 1);
        for (int i = 0; i < limit; ++i) {
            stack[i] += val;
        }
    }
```



**复杂度分析**

- 时间复杂度：初始化（构造函数）、`push` 操作和 `pop` 操作的渐进时间复杂度为 O(1)，`inc` 操作的渐进时间复杂度为 O(k)。
- 空间复杂度：这里用到了一个长度为 `maxSize` 的数组作为辅助空间，渐进空间复杂度为 O(maxSize)。

# 题解二 优化

用一个辅助数组 `add` 记录每次 `inc` 操作，将 `add[k - 1]` 增加 `val`。

这样做的目的在于，只有在 `pop` 操作时，我们才需要知道栈顶元素的具体值，在其余的情况下，我们只要存储每个元素的增量就行了。

因此在遇到 `pop` 操作时，我们返回栈顶元素的初始值加上增量 `add[top]`。在这之后，我们将增量向栈底进行传递，累加至 `add[top - 1]` 处，这样 `inc` 操作的时间复杂度也减少至 O(1) 了。

```java
    int[] stack;
    int[] add;
    int top;

    public CustomStack(int maxSize) {
        stack = new int[maxSize];
        add = new int[maxSize];
        top = -1;
    }
    
    public void push(int x) {
        if (top != stack.length - 1) {
            ++top;
            stack[top] = x;
        }
    }
    
    public int pop() {
        if (top == -1) {
            return -1;
        }
        int ret = stack[top] + add[top];
        if (top != 0) {
            add[top - 1] += add[top];
        }
        add[top] = 0;
        --top;
        return ret;
    }
    
    public void increment(int k, int val) {
        int limit = Math.min(k - 1, top);
        if (limit >= 0) {
            add[limit] += val;
        }
    }
```

**复杂度分析**

- 时间复杂度：所有操作的渐进时间复杂度均为 O(1)。
- 空间复杂度：这里用到了两个长度为 `maxSize` 的数组作为辅助空间，渐进空间复杂度为 O(maxSize)。