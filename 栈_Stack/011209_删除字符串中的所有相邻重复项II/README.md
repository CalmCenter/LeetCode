给你一个字符串 `s`，「`k` 倍重复项删除操作」将会从 `s` 中选择 `k` 个相邻且相等的字母，并删除它们，使被删去的字符串的左侧和右侧连在一起。

你需要对 `s` 重复进行无限次这样的删除操作，直到无法继续为止。

在执行完所有删除操作后，返回最终得到的字符串。

本题答案保证唯一。

**示例 1：**

```
输入：s = "abcd", k = 2
输出："abcd"
解释：没有要删除的内容。
```

**示例 2：**

```
输入：s = "deeedbbcccbdaa", k = 3
输出："aa"
解释： 
先删除 "eee" 和 "ccc"，得到 "ddbbbdaa"
再删除 "bbb"，得到 "dddaa"
最后删除 "ddd"，得到 "aa"
```

**示例 3：**

```
输入：s = "pbbcggttciiippooaais", k = 2
输出："ps"
```

**提示：**

- `1 <= s.length <= 10^5`
- `2 <= k <= 10^4`
- `s` 中只含有小写英文字母。

# 题解一 暴力解法

1. 记录字符串的长度。
2. 遍历字符串：
   - 如果当前字符与前一个相同，计数器加 1。
     - 否则，重置计数器为 `1`。
   - 如果计数器等于 `k`，删除这 `k` 个字符。
3. 如果字符串的长度被改变，从头开始重新遍历字符串。

```java
    public String removeDuplicates(String s, int k) {
        StringBuilder sb = new StringBuilder(s);
        int length = -1;
        while (length != sb.length()) {
            length = sb.length();
            for (int i = 0, count = 1; i < sb.length(); ++i) {
                // 如果当前字符不等于前一个，count 重置
                if (i == 0 || sb.charAt(i) != sb.charAt(i - 1)) {
                    count = 1;
                } else if (++count == k) {// 如果当前字符与前一个相同，则 count +1
                    // 如果 count + 1 后 等于 k 则删去 最近的三个字符
                    sb.delete(i - k + 1, i + 1);
                    break;
                }
            }
        }
        return sb.toString();
    }
```

**复杂度分析**

- 时间复杂度：O(n2/k)，其中 n 是字符串的长度。字符串扫描不超过 n / k 次。
- 空间复杂度：O(1)。某些语言会创建字符串的副本，但算法只在字符串本身上操作。

# 题解二 记忆计数

从方法一中可以看出，如果为每个字符设置计数器，就不必每次删除完字符后从头开始。这种方法具有线性复杂度，但需要额外空间存储字符的计数器。

- 初始长度为 `n` 的数组 `counts`。
- 遍历字符串：
  - 如果当前字符与上一个字符相等，令 `counts[i] = counts[i - 1] + 1`。
    - 否则，令 `counts[i] = 1`。
  - 如果 `counts[i] = k`，删除这 `k` 个字符，令 `i = i - k`。

```java
    public String removeDuplicates(String s, int k) {
        StringBuilder sb = new StringBuilder(s);
        int count[] = new int[sb.length()];
        for (int i = 0; i < sb.length(); ++i) {
            // 如果字符不同则在当前下标赋值为 1
            if (i == 0 || sb.charAt(i) != sb.charAt(i - 1)) {
                count[i] = 1;
            } else {
                // 如果相同，则在当前下标赋值前一个 下标的值 + 1
                count[i] = count[i - 1] + 1;
                // 如果 值为 k 删除近三个字符
                if (count[i] == k) {
                    sb.delete(i - k + 1, i + 1);
                    i = i - k;
                }
            }
        }
        return sb.toString();
    }
```

**复杂度分析**

- 时间复杂度：O(n)，其中 n 是字符串长度。每个字符仅被处理一次。
- 空间复杂度：O(n)，存储每个字符的计数器。

# 题解三 栈

当前字符与前一个不同时，往栈中压入 `1`。否则栈顶元素加 `1`。

1. 迭代字符串：
   1. 如果当前字符与前一个相同，栈顶元素加 1。
   2. 否则，往栈中压入 `1`。
2. 如果栈顶元素等于 `k`，则从字符串中删除这 `k` 个字符，并将 `k` 从栈顶移除。

> 注意：因为在 Java 中 `Integer` 是不可变的，需要先弹出栈顶元素，然后加 1，再压入栈顶。

```java
    public String removeDuplicates3(String s, int k) {
        StringBuilder sb = new StringBuilder(s);
        Deque<Integer> counts = new LinkedList<>();
        for (int i = 0; i < sb.length(); ++i) {
            if (i == 0 || sb.charAt(i) != sb.charAt(i - 1)) {
                counts.push(1);
            } else {
                int incremented = counts.pop() + 1;
                if (incremented == k) {
                    sb.delete(i - k + 1, i + 1);
                    i = i - k;
                } else {
                    counts.push(incremented);
                }
            }
        }
        return sb.toString();
    }
```

**复杂度分析**

- 时间复杂度：O(n)，其中 n 是字符串长度。每个字符只处理一次。
- 空间复杂度：O(n)，栈空间。

