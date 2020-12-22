
使用队列实现栈的下列操作：

- push(x) -- 元素 x 入栈
- pop() -- 移除栈顶元素
- top() -- 获取栈顶元素
- empty() -- 返回栈是否为空

**注意:**

- 你只能使用队列的基本操作-- 也就是 `push to back`, `peek/pop from front`, `size`, 和 `is empty` 这些操作是合法的。
- 你所使用的语言也许不支持队列。 你可以使用 list 或者 deque（双端队列）来模拟一个队列 , 只要是标准的队列操作即可。
- 你可以假设所有操作都是有效的（例如, 对一个空的栈不会调用 pop 或者 top 操作）。

# 方法一 两个队列

栈是一种后进先出的数据结构，元素从顶端入栈，然后从顶端出栈。

队列是一种先进先出的数据结构，元素从后端入队，然后从前端出队。

为了满足栈的特性，即最后入栈的元素最先出栈，在使用队列实现栈时，应满足队列前端的元素是最后入栈的元素。可以使用两个队列实现栈的操作，其中 `queue1` 用于存储栈内的元素，`queue2` 作为入栈操作的辅助队列。

入栈操作时，首先将元素入队到 `queue2`，然后将 `queue1` 的全部元素依次出队并入队到 `queue2`，相当于每次将最新的元素先添加到 `queue2` ，然后再将旧的元素(`queue1`)依次添加到 `queue2` ，这样越新的元素会越在前端，也就越先出队。

```java
class MyStack {
    Queue<Integer> queue1;
    Queue<Integer> queue2;

    public MyStack() {
        queue1 = new LinkedList<>();
        queue2 = new LinkedList<>();
    }

    public void push(int x) {
        queue2.offer(x);
        while (!queue1.isEmpty()) {
            queue2.offer(queue1.poll());
        }
        Queue<Integer> temp = queue1;
        queue1 = queue2;
        queue2 = temp;
    }

    public int pop() {
        return queue1.poll();
    }

    public int top() {
        return queue1.peek();
    }

    public boolean empty() {
        return queue1.isEmpty();
    }
}
```

**复杂度分析**

- 时间复杂度：入栈操作 O(n)，其余操作都是 *O*(1)。
- 空间复杂度：O(n)

# 方法二 一个队列

方法一使用了两个队列实现栈的操作，也可以使用一个队列实现栈的操作。

- 入栈操作时，首先获得入栈前的元素个数 n
- 然后将元素入队到队列，再将队列中的前 n 个元素（即除了新入栈的元素之外的全部元素），**依次出队然后重新入队到队列**
- 此时队列的前端的元素即为新入栈的元素，且队列的前端和后端分别对应栈顶和栈底。

```java
class MyStack {
    Queue<Integer> queue;

    public MyStack() {
        queue = new LinkedList<Integer>();
    }

    public void push(int x) {
        int n = queue.size();
        queue.offer(x);
        for (int i = 0; i < n; i++) {
            queue.offer(queue.poll());
        }
    }

    public int pop() {
        return queue.poll();
    }

    public int top() {
        return queue.peek();
    }

    public boolean empty() {
        return queue.isEmpty();
    }
}
```

**复杂度分析**

- 时间复杂度：入栈操作 O(n)，其余操作都是 O(1)
- 空间复杂度：O(n)，其中 n 是栈内的元素。需要使用一个队列存储栈内的元素。



