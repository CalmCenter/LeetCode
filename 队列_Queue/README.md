# 基础

如何理解 “队列” ？可以把它想象成排队买票，先来的先买，后来的人只能站末尾，不允许插队。**先进者先出**，这就是典型的“队列”。

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

这时我们可以在入队时，触发一次数据的搬移操作。

```java

   // 入队操作，将item放入队尾
  public boolean enqueue(String item) {
    // tail == n表示队列末尾没有空间了
    if (tail == n) {
      // tail ==n && head==0，表示整个队列都占满了
      if (head == 0) return false;
      // 数据搬移
      for (int i = head; i < tail; ++i) {
        items[i-head] = items[i];
      }
      // 搬移完之后重新更新head和tail
      tail -= head;
      head = 0;
    }
    
    items[tail] = item;
    ++tail;
    return true;
  }
```

从代码中我们看到，当队列的 `tail` 指针移动到数组的最右边后，如果有新的数据入队，我们可以将 `head` 到 `tail` 之间的数据，整体搬移到数组中 `0` 到 `tail-head` 的位置。

这种实现思路中，出队操作的时间复杂度仍然是 `O(1)`，入队操作的时间复杂度通过均摊时间也是 `O(1)`。

对于基于链表的实现，同样需要两个指针：`head` 指针和 `tail` 指针。它们分别指向链表的第一个结点和最后一个结点。如图所示，入队时，`tail->next= new_node`， `tail = tail->next`；出队时，`head = head->next`。

## 循环队列

刚才用数组来实现队列的时候，在 `tail==n` 时，会有数据搬移操作，这样入队操作性能就会受到影响。循环队列就可以有效的解决这一问题

循环队列，顾名思义，它长得像一个环。原本数组是有头有尾的，是一条直线。现在们把首尾相连，扳成了一个环。

```
 0 1 2 3 4 5 6
 _ _ _ a b c d
 ↑     ↑       
tail  head    
```

当我们添加完元素 `d` 后，`tail` 移动到下标为 `0` 的地方，意思就是末尾和头部是相连的。通过这样的方法，成功避免了数据搬移操作。

要想写出没有 `bug` 的循环队列的实现代码，最关键的是，确定好队空和队满的判定条件。

在用数组实现的**非循环队列**中，队满的判断条件是 `tail == n`，队空的判断条件是 `head == tail`。

**循环队列**中，队列为空的判断条件仍然是 `head == tail`。但是这是发现，队满的判断也是 `head == tail`，这肯定是有问题的，这是我们就要做出一定的牺牲。

```
 0 1 2 3 4 5 6
 e f _ a b c d
     ↑ ↑       
     t h   
```

当队列成为这种情况时，我们就得认为他是队满状态，`( 2 + 1 ) % 6 = 3` ，也就是 `( tail + 1 ) % n = head` ，所以，循环队列会浪费一个数组的存储空间。

```java

public class CircularQueue {
  // 数组：items，数组大小：n
  private String[] items;
  private int n = 0;
  // head表示队头下标，tail表示队尾下标
  private int head = 0;
  private int tail = 0;

  // 申请一个大小为capacity的数组
  public CircularQueue(int capacity) {
    items = new String[capacity];
    n = capacity;
  }

  // 入队
  public boolean enqueue(String item) {
    // 队列满了
    if ((tail + 1) % n == head) return false;
    items[tail] = item;
    tail = (tail + 1) % n;
    return true;
  }

  // 出队
  public String dequeue() {
    // 如果head == tail 表示队列为空
    if (head == tail) return null;
    String ret = items[head];
    head = (head + 1) % n;
    return ret;
  }
}
```

**队列使用场景**：**对于大部分资源有限的场景，当没有空闲资源时，基本上都可以通过“队列”这种数据结构来实现请求排队。**

一般有两种处理策略。第一种是非阻塞的处理方式，直接拒绝任务请求；另一种是阻塞的处理方式，将请求排队，等到有空闲线程时，取出排队的请求继续处理。

先进者先服务，所以队列这种数据结构很适合来存储排队请求。

**基于链表**的实现方式，可以实现一个支持无限排队的无界队列（`unbounded queue`），但是可能会导致过多的请求排队等待，请求处理的响应时间过长。所以，针对响应时间比较敏感的系统，基于链表实现的无限排队的线程池是不合适的。

而**基于数组**实现的有界队列（`bounded queue`），队列的大小有限，所以线程池中排队的请求超过队列大小时，接下来的请求就会被拒绝，这种方式对响应时间敏感的系统来说，就相对更加合理。不过，设置一个合理的队列大小，也是非常有讲究的。队列太大导致等待的请求太多，队列太小会导致无法充分利用系统资源、发挥最大性能。

# 题目整理

[010199_二叉树的右视图](https://github.com/CalmCenter/LeetCode/tree/master/队列_Queue/010199_二叉树的右视图) `深度优先搜索 ` `广度优先搜索` 

