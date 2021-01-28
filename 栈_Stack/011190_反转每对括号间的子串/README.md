给出一个字符串 `s`（仅含有小写英文字母和括号）。

请你按照从括号内到外的顺序，逐层反转每对匹配括号中的字符串，并返回最终的结果。

注意，您的结果中 **不应** 包含任何括号。

**示例 1：**

```
输入：s = "(abcd)"
输出："dcba"
```

**示例 2：**

```
输入：s = "(u(love)i)"
输出："iloveu"
```

**示例 3：**

```
输入：s = "(ed(et(oc))el)"
输出："leetcode"
```

**示例 4：**

```
输入：s = "a(bcdefghijkl(mno)p)q"
输出："apmnolkjihgfedcbq"
```

**提示：**

- `0 <= s.length <= 2000`
- `s` 中只有小写英文字母和括号
- 我们确保所有括号都是成对出现的

# 题解一 暴力

使用栈去匹配左括号，不断的反转两个括号之间的内容，最后输出的时候省略掉左括号和右括号。

```java
    public String reverseParentheses(String s) {
        Deque<Integer> stack = new LinkedList<>();
        StringBuilder res = new StringBuilder();
        char[] chs = s.toCharArray();

        for (int i = 0; i < chs.length; i++) {
            // 记录括号位置
            if (chs[i] == '(') {
                stack.push(i);
            } else if (chs[i] == ')') {
                // 反转括号内的内容
                reverse(chs, stack.pop() + 1, i - 1);
            }
        }
        // 将反转后的内容 去除 括号
        for (char ch : chs) {
            if (ch != '(' && ch != ')') {
                res.append(ch);
            }
        }
        return res.toString();
    }

    private void reverse(char[] chs, int start, int end) {
        while (end > start) {
            char temp = chs[end];
            chs[end] = chs[start];
            chs[start] = temp;
            start++;
            end--;
        }
    }
```

**复杂度分析**

时间复杂度：O(N^2)

空间复杂度：O(N)

## 题解二 虫洞法

![leetcode1190.gif](https://pic.leetcode-cn.com/10166eb49e485ee1b9abb5a32d4d1a20f4f3bb3840dc696ebf658194582347a2-leetcode1190.gif)

```java
    public String reverseParentheses(String s) {
        int n = s.length();
        Deque<Integer> stack = new LinkedList<>();
        int[] pair = new int[n];

        // 先去找匹配的括号，记录每个括号另一半对应的下标
        for (int i = 0; i < n; i++) {
            if (s.charAt(i) == '(') {
                stack.push(i);
            } else if (s.charAt(i) == ')') {
                int j = stack.pop();
                pair[i] = j;
                pair[j] = i;
            }
        }

        StringBuilder res = new StringBuilder();
        // i是当前位置 | d 是方向,1 就是向右移动
        for (int i = 0, d = 1; i < n; i+=d) {
            if (s.charAt(i) == '(' || s.charAt(i) == ')') {
                // 如果碰到括号，那么去他对应的括号，并且将方向置反
                i = pair[i];
                d = -d;
            } else {
                res.append(s.charAt(i));
            }
        }
        return res.toString();
    }
```

**复杂度分析**

时间复杂度：O(N)

空间复杂度：O(N)