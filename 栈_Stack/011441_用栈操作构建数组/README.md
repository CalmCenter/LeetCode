给你一个目标数组 `target` 和一个整数 `n`。每次迭代，需要从 `list = {1,2,3..., n}` 中依序读取一个数字。

请使用下述操作来构建目标数组 `target` ：

- **Push**：从 `list` 中读取一个新元素， 并将其推入数组中。
- **Pop**：删除数组中的最后一个元素。
- 如果目标数组构建完成，就停止读取更多元素。

题目数据保证目标数组严格递增，并且只包含 `1` 到 `n` 之间的数字。

请返回构建目标数组所用的操作序列。

题目数据保证答案是唯一的。

**示例 1：**

```
输入：target = [1,3], n = 3
输出：["Push","Push","Pop","Push"]
解释： 
读取 1 并自动推入数组 -> [1]
读取 2 并自动推入数组，然后删除它 -> [1]
读取 3 并自动推入数组 -> [1,3]
```

**示例 2：**

```
输入：target = [1,2,3], n = 3
输出：["Push","Push","Push"]
```

**示例 3：**

```
输入：target = [1,2], n = 4
输出：["Push","Push"]
解释：只需要读取前 2 个数字就可以停止。
```

**示例 4：**

```
输入：target = [2,3,4], n = 4
输出：["Push","Pop","Push","Push","Push"]
```

**提示：**

- `1 <= target.length <= 100`
- `1 <= target[i] <= 100`
- `1 <= n <= 100`
- `target` 是严格递增的

# 题解一 栈

因为是栈类的题目，强行用栈。

```java
    public List<String> buildArray(int[] target, int n) {
        Deque<Integer> stack = new LinkedList<>();
        List<String> list = new ArrayList<>();
        int index = 0;
        for (int i = 1; i <= n; i++) {
            if (index < target.length) {
                stack.push(i);
                list.add("Push");
            }
            // 如果 刚刚添加的字符 和 目标结果结果中当前下标的字符 不一样，证明该字符不再 目标结果 之中
            if (index < target.length && stack.peek() != target[index]) {
                stack.pop();
                list.add("Pop");
            } else {
                index++;
            }

        }
        return list;
    }
```

题解二 优化

由于上面使用栈保存了 `{1,2,3 ... n}` 还是按顺序保存的，完全可以用当前下标 `i` 表示 。

```java
    public List<String> buildArray2(int[] target, int n) {
        List<String> list = new ArrayList<>();
        for (int i = 1, index = 0; i <= n; i++) {
            if (index < target.length) {
                list.add("Push");
            }
            if (index < target.length && i != target[index]) {
                list.add("Pop");
            }else {
                index++;
            }
        }
        return list;
    }
```

