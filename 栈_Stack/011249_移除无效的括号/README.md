给你一个由 `'('`、`')'` 和小写字母组成的字符串 `s`。

你需要从字符串中删除最少数目的 `'('` 或者 `')'` （可以删除任意位置的括号)，使得剩下的「括号字符串」有效。

请返回任意一个合法字符串。

有效「括号字符串」应当符合以下 **任意一条** 要求：

- 空字符串或只包含小写字母的字符串
- 可以被写作 `AB`（`A` 连接 `B`）的字符串，其中 `A` 和 `B` 都是有效「括号字符串」
- 可以被写作 `(A)` 的字符串，其中 `A` 是一个有效的「括号字符串」

**示例 1：**

```
输入：s = "lee(t(c)o)de)"
输出："lee(t(c)o)de"
解释："lee(t(co)de)" , "lee(t(c)ode)" 也是一个可行答案。
```

**示例 2：**

```
输入：s = "a)b(c)d"
输出："ab(c)d"
```

**示例 3：**

```
输入：s = "))(("
输出：""
解释：空字符串也是有效的
```

**示例 4：**

```
输入：s = "(a(b(c)d)"
输出："a(b(c)d)"
```

**提示：**

- `1 <= s.length <= 10^5`
- `s[i]` 可能是 `'('`、`')'` 或英文小写字母

# 题解一 使用栈和 StringBuilder

```java
    public String minRemoveToMakeValid(String s) {
        Set<Integer> indexesToRemove = new HashSet<>();
        Deque<Integer> stack = new LinkedList<>();
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                // 进栈
                stack.push(i);
            }
            if (s.charAt(i) == ')') {
                // 如果是 ) 并且前面没有 ( 证明需要删除
                if (stack.isEmpty()) {
                    indexesToRemove.add(i);
                } else {
                    // 找到配对的 出栈
                    stack.pop();
                }
            }
        }
        // 没有配对上的 也是要删除的
        while (!stack.isEmpty()) indexesToRemove.add(stack.pop());
        // 重新组织字符串，不添加删除的字符
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < s.length(); i++) {
            if (!indexesToRemove.contains(i)) {
                sb.append(s.charAt(i));
            }
        }
        return sb.toString();
    }
```

**复杂度分析**

- 时间复杂度：O(n)，其中 n 是输入字符串的长度。
- 空间复杂度：O(n)

# 题解二 使用 StringBuilder 的两步法

从方法一中可知，根据栈中是否有可匹配的 `"(" 开始括号`，可以立即知道当前每个 `")" 结束括号` 是否有效。

但是无法立即知道每个 `"("` 是否有效，必须要等到字符串扫描结束，根据栈中是否有剩余的 `"("` 确定。

就可以翻转字符，反转后 `"("` 作为了结束括号，就可以去匹配 `")" 开始括号` ，匹配不上就是无效

```java
    private StringBuilder removeInvalidClosing(CharSequence string, char open, char close) {
        StringBuilder sb = new StringBuilder();
        int balance = 0;// 记录有多少个开始括号
        for (int i = 0; i < string.length(); i++) {
            char c = string.charAt(i);
            if (c == open) {
                balance++;
            } if (c == close) {
                // 没有开始括号 证明无效
                if (balance == 0) continue;
                balance--;
            }
            sb.append(c);
        }
        return sb;
    }

    public String minRemoveToMakeValid2(String s) {
        // 删除无效的 )
        StringBuilder result = removeInvalidClosing(s, '(', ')');
        // 反转字符串 删除无效的 (
        result = removeInvalidClosing(result.reverse(), ')', '(');
        return result.reverse().toString();
    }
```

**复杂度分析**

- 时间复杂度：O(n)
- 空间复杂度：O(n)

# 题解三 改进的使用 StringBuilder 的两步法

这是方法二的简化版本，只需要保持平衡即可，不需要栈，也不需要执行两次完整的字符串扫描。在完成第一步扫描后，查看有多少个需要删除的 "("，然后从右侧开始扫描，删除对应个数的 "(" 即可。

```java
    public String minRemoveToMakeValid3(String s) {

        StringBuilder sb = new StringBuilder();
        int openSeen = 0;
        int balance = 0;
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            // 记录开始括号 和 未成对括号 数量
            if (c == '(') {
                openSeen++;
                balance++;
            } if (c == ')') {
                // 未成对括号 为 0 ，不做操作 否则 未成对 -1
                if (balance == 0) continue;
                balance--;
            }
            sb.append(c);
        }

        StringBuilder result = new StringBuilder();
        // 有效的 开始括号 (
        int openToKeep = openSeen - balance;
        for (int i = 0; i < sb.length(); i++) {
            char c = sb.charAt(i);
            if (c == '(') {
                openToKeep--;
                // 没有有效开始括号后 不再添加 开始括号
                if (openToKeep < 0) continue;
            }
            result.append(c);
        }

        return result.toString();
    }
```

**复杂度分析**

- 时间复杂度：O(n)
- 空间复杂度：O(n)