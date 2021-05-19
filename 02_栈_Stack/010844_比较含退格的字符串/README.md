给定 `S` 和 `T` 两个字符串，当它们分别被输入到空白的文本编辑器后，判断二者是否相等，并返回结果。`#` 代表退格字符。

**注意：**如果对空文本输入退格字符，文本继续为空。

**示例 1：**

```
输入：S = "ab#c", T = "ad#c"
输出：true
解释：S 和 T 都会变成 “ac”。
```

**示例 2：**

```
输入：S = "ab##", T = "c#d#"
输出：true
解释：S 和 T 都会变成 “”。
```

**示例 3：**

```
输入：S = "a##c", T = "#a#c"
输出：true
解释：S 和 T 都会变成 “c”。
```

**示例 4：**

```
输入：S = "a#c", T = "b"
输出：false
解释：S 会变成 “c”，但 T 仍然是 “b”。
```

**提示：**

1. `1 <= S.length <= 200`
2. `1 <= T.length <= 200`
3. `S` 和 `T` 只含有小写字母以及字符 `'#'`。

# 题解一 重构字符串

- 如果它是退格符，那么删除最后一位；
- 如果它是普通字符，那么添加。

```java
    public boolean backspaceCompare(String S, String T) {
        return build(S).equals(build(T));
    }

    public String build(String str) {
        // 重新组织字符
        StringBuffer ret = new StringBuffer();
        int length = str.length();
        for (int i = 0; i < length; ++i) {
            char ch = str.charAt(i);
            if (ch != '#') {
                // 不是 # 则添加当前字符
                ret.append(ch);
            } else {
                // 如果是 # 则删除最后一位
                if (ret.length() > 0) {
                    ret.deleteCharAt(ret.length() - 1);
                }
            }
        }
        return ret.toString();
    }
```

**复杂度分析**

时间复杂度：O(N+M)，其中 N 和 M 分别为字符串 S 和 T 的长度。我们需要遍历两字符串各一次。

空间复杂度：O(N+M)，其中 N 和 M 分别为字符串 S 和 T 的长度。主要为还原出的字符串的开销。

# 题解二 双指针

```java
    public boolean backspaceCompare2(String S, String T) {
        // i ：S 的指针下标  j：T 的指针下标
        int i = S.length() - 1, j = T.length() - 1;
        int skipS = 0, skipT = 0;

        while (i  >= 0 || j >= 0) {
            while (i >= 0) {
                // 是否 有 #
                if (S.charAt(i) == '#') {
                    skipS++;
                    // 跳过当前的 #
                    i--;
                } else if (skipS > 0) {
                    // 当前一次出现 # 那么会到这里
                    skipS--;
                    // 跳过 skipS 个字符
                    i--;
                } else {
                    break;
                }
            }
            // 第二个字符同理
            while (j >= 0) {
                if (T.charAt(j) == '#') {
                    skipT++;
                    j--;
                } else if (skipT > 0) {
                    skipT--;
                    j--;
                } else {
                    break;
                }
            }
            // 判断长度是否一样
            if (i >= 0 && j >= 0) {
                // 判断当前下标的字符是否一样
                if (S.charAt(i) != T.charAt(j)) {
                    return false;
                }
            } else {
                // 判断长度是否一样
                if (i >= 0 || j >= 0) {
                    return false;
                }
            }
            // 下标左移
            i--;
            j--;
        }
        return true;
    }
```

**复杂度分析**

时间复杂度：O(N+M)，其中 N 和 M 分别为字符串 S 和 T 的长度。我们需要遍历两字符串各一次。

空间复杂度：O(1)。对于每个字符串，我们只需要定义一个指针和一个计数器即可。

