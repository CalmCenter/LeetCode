给你一个由大小写英文字母组成的字符串 `s` 。

一个整理好的字符串中，两个相邻字符 `s[i]` 和 `s[i+1]`，其中 `0<= i <= s.length-2` ，要满足如下条件:

- 若 `s[i]` 是小写字符，则 `s[i+1]` 不可以是相同的大写字符。
- 若 `s[i]` 是大写字符，则 `s[i+1]` 不可以是相同的小写字符。

请你将字符串整理好，每次你都可以从字符串中选出满足上述条件的 **两个相邻** 字符并删除，直到字符串整理好为止。

请返回整理好的 **字符串** 。题目保证在给出的约束条件下，测试样例对应的答案是唯一的。

**注意：**空字符串也属于整理好的字符串，尽管其中没有任何字符。

**示例 1：**

```
输入：s = "leEeetcode"
输出："leetcode"
解释：无论你第一次选的是 i = 1 还是 i = 2，都会使 "leEeetcode" 缩减为 "leetcode" 。
```

**示例 2：**

```
输入：s = "abBAcC"
输出：""
解释：存在多种不同情况，但所有的情况都会导致相同的结果。例如：
"abBAcC" --> "aAcC" --> "cC" --> ""
"abBAcC" --> "abBA" --> "aA" --> ""
```

**示例 3：**

```
输入：s = "s"
输出："s"
```

**提示：**

- `1 <= s.length <= 100`
- `s` 只包含小写和大写英文字母

# 题解

```java
    public String makeGood(String s) {
        int idx = -1;
        char[] ss = s.toCharArray();
        for (int i = 0; i < ss.length; ++i) {
            // idx 表示符合规则的字符长度
            if (idx == -1 || !isDelete(ss[i], ss[idx])) {
                // 将符合规则的字符 添加在 idx 下标后面
                ss[++idx] = ss[i];
            } else {
                // 不符合规则 长度减少 之前的字符将被替换
                --idx;
            }
        }
        return String.valueOf(Arrays.copyOfRange(ss, 0, idx + 1));
    }

    public boolean isDelete(char c1, char c2) {
        // 相同 字符大小写之间  相差 32
        return Math.abs(c1 - c2) == 32;
    }
```

**复杂度分析**

- 时间复杂度：O(N)，其中 N 为字符串 `s` 的长度。我们要遍历字符串 `s` 的每一个字符，而对每个字符都只需要常数时间的操作。
- 空间复杂度： O(1)