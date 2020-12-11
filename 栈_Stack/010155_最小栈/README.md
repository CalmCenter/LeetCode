设计一个支持 `push` ，`pop` ，`top` 操作，并能在常数时间内检索到最小元素的栈。

- `push(x)` —— 将元素 x 推入栈中。
- `pop()` —— 删除栈顶的元素。
- `top()` —— 获取栈顶元素。
- `getMin()` —— 检索栈中的最小元素。

**示例:**

```
输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

输出：
[null,null,null,null,-3,null,0,-2]

解释：
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```

**提示：**

- `pop`、`top` 和 `getMin` 操作总是在 **非空栈** 上调用。

# 方法一

```java
    Deque<Integer> xStack;
    Deque<Integer> minStack;

    public MinStack() {
        xStack = new LinkedList<Integer>();
        minStack = new LinkedList<Integer>();
        minStack.push(Integer.MAX_VALUE);
    }
    
    public void push(int x) {
        xStack.push(x);
        minStack.push(Math.min(minStack.peek(), x));
    }
    
    public void pop() {
        xStack.pop();
        minStack.pop();
    }
    
    public int top() {
        return xStack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }

```

复杂度分析

时间复杂度：对于题目中的所有操作，时间复杂度均为O(1)。因为栈的插入、删除与读取操作都是 O(1)，我们定义的每个操作最多调用栈操作两次。

空间复杂度：O(n)，其中 n 为总操作数。最坏情况下，我们会连续插入 n 个元素，此时两个栈占用的空间为 O(n)。

# 方法二

`stack` 用来存储和 `min` 的差值，`min` 存储最小值，每次出栈的时候通过差值和当前 `min` 计算要出栈的值和之前的 `min` 

如果差值 `diff` 大于等于 `0` ，说明要出栈的值大于等于当前 `min` ，那么要出栈的值在入栈的时候没有更新 `min` ，返回 `min+diff` ；如果插值 `diff` 小于 `0`，说明当前要出栈的值就是 `min` (因为入栈的时候我们选择的就是 `min` 和入栈元素的最小值)，同时，通过 `min-diff` 计算出之前 `min` 要注意的是 `diff` 可能会超出 `int` 范围，类似于 `Integer.MAX_VALUE - 1` 这种，所以 `diff` 要用 `Long` 存

```java
    Integer min = null;
    Deque<Long> data = new LinkedList<>();

    /**
     * initialize your data structure here.
     */
    public MinStack() {

    }

    public void push(int x) {
        if (data.isEmpty()) {
            data.push(0L);
            min = x;
        } else {
            //如果 x 是最小的数，这里可能越界，所以用Long来保存
            data.push((long) x - min);
            min = Math.min(x, min);
        }
    }

    public void pop() {
        Long diff = data.pop();
        if (diff >= 0) {
            //return min + diff;
        } else {
            int res = min;
            min = (int) (min - diff);
            //return res;
        }
    }

    public int top() {
        Long diff = data.peek();
        if (diff >= 0) {
            return (int) (min + diff);
        } else {
            return min;
        }
    }

    public int getMin() {
        return min;
    }
```

复杂度分析

时间复杂度：O(1)

空间复杂度：O(1)