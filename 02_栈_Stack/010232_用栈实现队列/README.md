请你仅使用两个栈实现先入先出队列。队列应当支持一般队列的支持的所有操作（`push`、`pop`、`peek`、`empty`）：

实现 `MyQueue` 类：

- `void push(int x)` 将元素 x 推到队列的末尾
- `int pop()` 从队列的开头移除并返回元素
- `int peek()` 返回队列开头的元素
- `boolean empty()` 如果队列为空，返回 `true` ；否则，返回 `false`

**说明：**

- 你只能使用标准的栈操作 —— 也就是只有`push to top`, `peek/pop from top`, `size`, 和 `is empty` 操作是合法的。
- 你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。

**进阶：**

- 你能否实现每个操作均摊时间复杂度为 `O(1)` 的队列？换句话说，执行 `n` 个操作的总时间复杂度为 `O(n)` ，即使其中一个操作可能花费较长时间。

**示例：**

```java
输入：
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
输出：
[null, null, null, 1, 1, false]

解释：
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false
```

**提示：**

- `1 <= x <= 9`
- 最多调用 `100` 次 `push`、`pop`、`peek` 和 `empty`
- 假设所有操作都是有效的 （例如，一个空的队列不会调用 `pop` 或者 `peek` 操作）



队列是一种 **先进先出**（first in - first out， FIFO）的数据结构，队列中的元素都从后端（rear）入队（push），从前端（front）出队（pop）。

实现队列最直观的方法是用链表，但在这篇文章里我会介绍另一个方法 - 使用栈。

栈是一种 **后进先出**（last in - first out， LIFO）的数据结构，栈中元素从栈顶（top）压入（push)，也从栈顶弹出（pop）。

为了满足队列的 **FIFO** 的特性，我们需要用到两个栈，用它们其中一个来反转元素的入队顺序，用另一个来存储元素的最终顺序。

## 方法一（使用两个栈 入队 - O(n)， 出队 - O(1)）

如果一个栈 `(s2)` 压入数据的顺序为 `(底)1、2、3、4(顶)` ， 那么将这个 `s2` 出栈重新压入 `s1` 中 `(底)4、3、2、1(顶)`  ，那么这个 `s1`  的出栈就和队列的出队性质一样了，先进先出。

每次压入数据先压入 `s2` ，再将 `s2` 的数据全部压入 `s1` 

```java
    Deque<Integer> s1 = new LinkedList<>();
    Deque<Integer> s2 = new LinkedList<>();

    public MyQueue() {

    }

    // 记录顶层元素
    private int front;

    public void push(int x) {
        // 证明没有数据
        if (s1.isEmpty())
            front = x;
        // 将 s1 的数据压入 s2 现在是正常栈的特性
        while (!s1.isEmpty())
            s2.push(s1.pop());
        // 添加新数据
        s2.push(x);
        // 将 s2 的数据压入 s1 ， s1 就实现了队列的特性
        while (!s2.isEmpty())
            s1.push(s2.pop());
    }

    public int pop() {
        Integer pop = s1.pop();
        if (!s1.isEmpty())
            front = s1.peek();
        return pop;
    }

    public int peek() {
        return front;
    }

    public boolean empty() {
        return s1.isEmpty();
    }
```

**复杂度分析**

- 时间复杂度：`push` 为 O(n) ，其余都为 O(1)
- 空间复杂度：`push` 为 O(n) ，其余都为 O(1)

## 方法二（使用两个栈 入队 - O(1)，出队 - 摊还复杂度 O(1)）

```java

    Deque<Integer> s1 = new LinkedList<>();
    Deque<Integer> s2 = new LinkedList<>();

    public MyQueue2() {

    }

    // 记录顶层元素
    private int front;

    public void push(int x) {
        // s1 正常添加，只记录 前端即将出队的元素
        if (s1.isEmpty())
            front = x;
        s1.push(x);
    }

    // 需要出队时在做处理，将 s1 反转到 s2 进行出队，s2 的数据将变成 队列前端的几个数据
    // 只需要一次，反转后 s2 的出队顺序不会变，
    // 如果再添加数据需要将原先 s2 的数据都出队完，再将 s1 新添加的数据反转到 s2
    public int pop() {
        if (s2.isEmpty()) {
            while (!s1.isEmpty())
                s2.push(s1.pop());
        }
        return s2.pop();
    }

    // 先判断 s2 是否有值，因为可能出现 pop 之后再 push 数据
    // 这样 front 将是刚 push 的数据，所以必须先判断 s2 是否有值
    // s2 为 队列前端的几个数据，可能不是全部的数据
    public int peek() {
        if (!s2.isEmpty()) {
            return s2.peek();
        }
        return front;
    }

    public boolean empty() {
        return s1.isEmpty() && s2.isEmpty();
    }
```

**复杂度分析**

- 时间复杂度：

  - **push**： (O1)

  - **pop**：摊还复杂度 O(1)，最坏情况下的时间复杂度 O(n)

    在最坏情况下，s2 为空，算法需要从 s1 中弹出 n 个元素，然后再把这 n 个元素压入 s2，在这里 n 代表队列的大小。这个过程产生了 2n 步操作，时间复杂度为 O(n)。但当 s2 非空时，算法就只有 O(1) 的时间复杂度。

  - **empty**：(O1)

  - **peek**：O(1) 队首元素要么是之前就被计算出来的，要么就是 `s2` 栈顶元素。因此时间复杂度为  *O*(1)。

- 空间复杂度：
  - **push**： (On)
  - **pop**：(O1)
  - **empty**：(O1)
  - **peek**：O(1) 