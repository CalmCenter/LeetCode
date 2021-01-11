给出一个非抢占单线程 `CPU` 的 **n** 个函数运行日志，找到函数的独占时间。

每个函数都有一个唯一的 `Id`，从 **0** 到 **n-1**，函数可能会递归调用或者被其他函数调用。

日志是具有以下格式的字符串：`function_id：start_or_end：timestamp`。例如：`"0:start:0"` 表示函数 0 从 0 时刻开始运行。`"0:end:0"` 表示函数 0 在 0 时刻结束。

函数的独占时间定义是在该方法中花费的时间，调用其他函数花费的时间不算该函数的独占时间。你需要根据函数的 `Id` 有序地返回每个函数的独占时间。

**示例 1:**

```
输入:
n = 2
logs = 
["0:start:0",
 "1:start:2",
 "1:end:5",
 "0:end:6"]
 输出:[3, 4]
说明：
函数 0 在时刻 0 开始，在执行了  2个时间单位结束于时刻 1。
现在函数 0 调用函数 1，函数 1 在时刻 2 开始，执行 4 个时间单位后结束于时刻 5。
函数 0 再次在时刻 6 开始执行，并在时刻 6 结束运行，从而执行了 1 个时间单位。
所以函数 0 总共的执行了 2 +1 =3 个时间单位，函数 1 总共执行了 4 个时间单位。
```

**说明：**

1. 输入的日志会根据时间戳排序，而不是根据日志Id排序。
2. 你的输出会根据函数Id排序，也就意味着你的输出数组中序号为 0 的元素相当于函数 0 的执行时间。
3. 两个函数不会在同时开始或结束。
4. 函数允许被递归调用，直到运行结束。
5. 1 <= n <= 100

# 题解

题目就是为了计算每个线程，各自独立执行了多长时间。

依次遍历所有的日志

- 对于第 `i` 条日志，如果它包含 `start`，那么栈顶函数从其时间戳 `time[i]` 开始运行，即 `prev = time[i]`；
- 如果它包含 `end`，那么栈顶函数从其时间戳 `time[i]` 的下一个时间开始运行，即 `prev = time[i] + 1`。
- 对于第 `i + 1` 条日志，如果它包含 `start`，那么在时间戳 `time[i + 1]` 时，有新的函数被调用，因此原来的栈顶函数的独占时间为 `time[i + 1] - prev`；
- 如果它包含 `end`，那么在时间戳 `time[i + 1]` 时，原来的栈顶函数执行结束，独占时间为 `time[i + 1] - prev + 1`。
- 在这之后，我们更新 `prev` 并遍历第 `i + 2` 条日志。在遍历结束后，我们就可以得到所有函数的独占时间。

单线程非抢占 `cpu` ，就是说父函数调用子函数，子函数结束之后、父函数才有可能结束。不会出现父函数结束了，子函数继续运行的情况。

```java
    public int[] exclusiveTime(int n, List<String> logs) {
        Deque<Integer> stack = new LinkedList<>();
        int[] res = new int[n];
        // 截取 id 状态 和 时间
        String[] s = logs.get(0).split(":");
        // id 入栈
        stack.push(Integer.parseInt(s[0]));
        // prev 时间
        int i = 1, prev = Integer.parseInt(s[2]);
        while (i < logs.size()) {
            // 截取 id 状态 和 时间
            s = logs.get(i).split(":");
            // 状态 如果是 start
            if (s[1].equals("start")) {
                // 有正在运行的线程
                if (!stack.isEmpty())
                    // 更新栈顶线程运行时间，当前 start 时间 - 前一个线程 开始/结束 时间
                    res[stack.peek()] += Integer.parseInt(s[2]) - prev;
                // 入栈 id
                stack.push(Integer.parseInt(s[0]));
                // 更新 prev 为 前一个线程开始时间
                prev = Integer.parseInt(s[2]);
            } else {
                // end 由于是 单线程非抢占 cpu 当前结束的 必定和栈顶 id 相同
                // 更新栈顶线程运行时间
                res[stack.peek()] += Integer.parseInt(s[2]) - prev + 1;
                stack.pop();
                // 更新 prev 为 前一个线程结束时间
                prev = Integer.parseInt(s[2]) + 1;
            }
            i++;
        }
        return res;
    }
```

复杂度分析

时间复杂度：O(N)。我们需要遍历所有的日志，因为有 N 个函数，因此日志的数量为 2N。

空间复杂度：O(N)，为栈占用的空间。

