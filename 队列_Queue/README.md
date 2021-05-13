# 基础

如何理解 “队列” ？可以把它想象成排队买票，先来的先买，后来的人只能站末尾，不允许插队。先进者先出，这就是典型的“队列”。

我们知道，栈只支持两个基本操作：入栈 `push()` 和出栈 `pop()` 。队列跟栈非常相似，支持的操作也很有限，最基本的操作也是两个：入队 `enqueue()`，放一个数据到队列尾部；出队 `dequeue()`，从队列头部取一个元素。

所以，队列跟栈一样，也是一种**操作受限的线性表数据结构**。

跟栈一样，队列可以用数组来实现，也可以用链表来实现。用数组实现的栈叫作**顺序栈**，用链表实现的栈叫作**链式栈**。同样，用数组实现的队列叫作顺序队列，用链表实现的队列叫作链式队列。

```java

// 用数组实现的队列
public class ArrayQueue {
  // 数组：items，数组大小：n
  private String[] items;
  private int n = 0;
  // head表示队头下标，tail表示队尾下标
  private int head = 0;
  private int tail = 0;

  // 申请一个大小为capacity的数组
  public ArrayQueue(int capacity) {
    items = new String[capacity];
    n = capacity;
  }

  // 入队
  public boolean enqueue(String item) {
    // 如果tail == n 表示队列已经满了
    if (tail == n) return false;
    items[tail] = item;
    ++tail;
    return true;
  }

  // 出队
  public String dequeue() {
    // 如果head == tail 表示队列为空
    if (head == tail) return null;
    // 为了让其他语言的同学看的更加明确，把--操作放到单独一行来写了
    String ret = items[head];
    ++head;
    return ret;
  }
}
```

对于栈来说，我们只需要一个**栈顶指针**就可以了。但是队列需要两个指针：一个是 `head` 指针，指向队头；一个是 `tail` 指针，指向队尾。

```
 0 1 2 3 4 5 6
 a b c d _ _ _
 ↑       ↑
head    tail
```

当 `a、b、c、d` 依次入队之后，队列中的 `head` 指针指向下标为 `0` 的位置，`tail` 指针指向下标为 `4` 的位置，准备下次填充。

当调用两次出队操作之后，队列中 `head` 指针指向下标为 `2` 的位置，`tail` 指针仍然指向下标为 `4` 的位置。

```
 0 1 2 3 4 5 6
 _ _ c d _ _ _
     ↑   ↑
   head tail
```

随着不停地进行入队、出队操作，`head` 和 ` tail` 都会持续往后移动。当 `tail` 移动到最右边，即使数组中还有空闲空间，也无法继续往队列中添加数据了。

这时我们可以在入队时，触发一次数据的搬移操作，。