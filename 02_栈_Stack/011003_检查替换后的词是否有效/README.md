给定有效字符串 `"abc"`。

对于任何有效的字符串 `V`，我们可以将 `V` 分成两个部分 `X` 和 `Y`，使得 `X + Y`（`X` 与 `Y` 连接）等于 `V`。（`X` 或 `Y` 可以为空。）那么，`X + "abc" + Y` 也同样是有效的。

例如，如果 `S = "abc"`，则有效字符串的示例是：`"abc"`，`"aabcbc"`，`"abcabc"`，`"abcabcababcc"`。**无效**字符串的示例是：`"abccba"`，`"ab"`，`"cababc"`，`"bac"`。

如果给定字符串 `S` 有效，则返回 `true`；否则，返回 `false`。

**示例 1：**

```
输入："aabcbc"
输出：true
解释：
从有效字符串 "abc" 开始。
然后我们可以在 "a" 和 "bc" 之间插入另一个 "abc"，产生 "a" + "abc" + "bc"，即 "aabcbc"。
```

**示例 2：**

```
输入："abcabcababcc"
输出：true
解释：
"abcabcabc" 是有效的，它可以视作在原串后连续插入 "abc"。
然后我们可以在最后一个字母之前插入 "abc"，产生 "abcabcab" + "abc" + "c"，即 "abcabcababcc"。
```

**示例 3：**

```
输入："abccba"
输出：false
```

**示例 4：**

```
输入："cababc"
输出：false
```

# 题解

这道题的题目描述一大堆，总结以下两点：

1. `abc` 是有效字符串，`abc` 的顺序是「相对有序」的
2. 在一个有效字符串的任意位置插入abc得到的字符串是有效字符串

```java
    public boolean isValid(String S) {
        while(S.contains("abc")) {
            S = S.replaceAll("abc","");
        }
        return S.equals("");
    }
```

# 题解二 栈

1. **用栈来解此题；**
2. **针对字母 c ，当前字母为 c 时,就 pop b and pop a，如果能正常执行，则无误，反之则有错；**
3. **最后判断栈是否为空即可；**

```java
    public boolean isValid(String S) {
        Deque<Character> stack = new LinkedList<>();
        for (int i = 0; i < S.length(); i++) {
            if (S.charAt(i) == 'c') {
                if (stack.isEmpty() || stack.pop() != 'b')
                    return false;
                if (stack.isEmpty() || stack.pop() != 'a')
                    return false;
            } else {
                stack.push(S.charAt(i));
            }
        }
        return stack.isEmpty();
    }
```

