设计实现双端队列。
你的实现需要支持以下操作：

- MyCircularDeque(k)：构造函数,双端队列的大小为k。
- insertFront()：将一个元素添加到双端队列头部。 如果操作成功返回 true。
- insertLast()：将一个元素添加到双端队列尾部。如果操作成功返回 true。
- deleteFront()：从双端队列头部删除一个元素。 如果操作成功返回 true。
- deleteLast()：从双端队列尾部删除一个元素。如果操作成功返回 true。
- getFront()：从双端队列头部获得一个元素。如果双端队列为空，返回 -1。
- getRear()：获得双端队列的最后一个元素。 如果双端队列为空，返回 -1。
- isEmpty()：检查双端队列是否为空。
- isFull()：检查双端队列是否满了。

**示例：**

```
MyCircularDeque circularDeque = new MycircularDeque(3); // 设置容量大小为3
circularDeque.insertLast(1);			        // 返回 true
circularDeque.insertLast(2);			        // 返回 true
circularDeque.insertFront(3);			        // 返回 true
circularDeque.insertFront(4);			        // 已经满了，返回 false
circularDeque.getRear();  				// 返回 2
circularDeque.isFull();				        // 返回 true
circularDeque.deleteLast();			        // 返回 true
circularDeque.insertFront(4);			        // 返回 true
circularDeque.getFront();				// 返回 4
```

**提示：**

- 所有值的范围为 [1, 1000]
- 操作次数的范围为 [1, 1000]
- 请不要使用内置的双端队列库。

# 题解

`front`永远指向队列的**头部元素**，`last`永远指向队**列尾部元素的下一个位置**。

- 对于`insertFront(int k)`方法，如果能够插入，需要判断下`front`指针的位置，如果是在数组`0`位置，那么需要插入的元素应该放在数组最后的位置，同时更改`front`指向的位置。否则的话，直接放在`front`前面那个位置即可。同时要维护`size`。
- 对于`deleteLast()`方法，如果能够插入，需要判断下`last`指针位置，如果是在数组`0`位置，所以需要删除的元素肯定是数组最后位置的元素，此时，只需让`last`指向数组最后一个位置即可。否则的话，直接`last--`就可以了。同时要维护`size`。

```java
class MyCircularDeque {

    int[] arr;
    int size;
    int front;
    int last;

    /** 构造方法设置双端队列的大小 */
    public MyCircularDeque(int k) {
        if (k < 0) {
            throw new IllegalArgumentException("Deque size is less than 0!");
        }
        this.arr = new int[k];
        this.size = 0;
        this.front = 0;
        this.last = 0;
    }

    /** 头部插入成功返回 true */
    public boolean insertFront(int value) {
        // 判断队列是否已满
        if (size == arr.length) {
            return false;
        }
        // front前移并插入元素
        if (front == 0) {
            arr[arr.length - 1] = value;
            front = arr.length - 1;
        } else {
            arr[--front] = value;
        }
        size ++;
        return true;
    }

    /** 尾部插入成功返回 true*/
    public boolean insertLast(int value) {
        if (size == arr.length) {
            return false;
        }
        arr[last] = value;
        size ++;
        last = last == arr.length - 1 ? 0 : last + 1;
        return true;
    }

    /** 头部出队 成功返回 true */
    public boolean deleteFront() {
        if (size == 0) {
            return false;
        }
        front = front == arr.length - 1 ? 0 : front + 1;
        size--;
        return true;
    }

    /** 尾部出队 成功返回 true */
    public boolean deleteLast() {
        if (size == 0) {
            return false;
        }
        if (last == 0) {
            last = arr.length - 1;
        } else {
            last --;
        }
        size --;
        return true;
    }

    /** 过去头部元素 */
    public int getFront() {
        if (size == 0) {
            return -1;
        }
        return arr[front];
    }

    /** 获取尾部元素. */
    public int getRear() {
        if (size == 0) {
            return -1;
        }
        return last == 0 ? arr[arr.length - 1] : arr[last - 1];
    }

    /** 队列是否为 空 */
    public boolean isEmpty() {
        return size == 0;
    }

    /** 队列是否已满 */
    public boolean isFull() {
        return size == arr.length;
    }

}
```

