给定一个表示代码片段的字符串，你需要实现一个验证器来解析这段代码，并返回它是否合法。合法的代码片段需要遵守以下的所有规则：

1. 代码必须被**合法的闭合标签**包围。否则，代码是无效的。

2. **闭合标签**（不一定合法）要严格符合格式：

   `<TAG_NAME>TAG_CONTENT</TAG_NAME>`。其中，`<TAG_NAME>`是起始标签，`</TAG_NAME>`是结束标签。起始和结束标签中的 `TAG_NAME` 应当相同。当且仅当 `TAG_NAME` 和 `TAG_CONTENT` 都是合法的，闭合标签才是**合法的**。

3. **合法的** `TAG_NAME` 仅含有**大写字母**，长度在范围 `[1,9]` 之间。否则，该 `TAG_NAME` 是**不合法的**。

4. **合法的** `TAG_CONTENT` 可以包含其他**合法的闭合标签**，**cdata** （请参考规则 7 ）和任意字符（注意参考规则1）**除了**不匹配的 `<`、不匹配的起始和结束标签、不匹配的或带有不合法 `TAG_NAME` 的闭合标签。否则，`TAG_CONTENT` 是**不合法的**。

5. 一个起始标签，如果没有具有相同 `TAG_NAME` 的结束标签与之匹配，是不合法的。反之亦然。不过，你也需要考虑标签嵌套的问题。

6. 一个 `<` ，如果你找不到一个后续的`>`与之匹配，是不合法的。并且当你找到一个 `<` 或 `</` 时，所有直到下一个 `>` 的前的字符，都应当被解析为 `TAG_NAME`（不一定合法）。

7. `cdata` 有如下格式：`<![CDATA[CDATA_CONTENT]]>`。`CDATA_CONTENT` 的范围被定义成 `<![CDATA[` 和**后续的第一个** `]]>` 之间的字符。

8. `CDATA_CONTENT` 可以包含**任意字符**。`cdata` 的功能是阻止验证器解析`CDATA_CONTENT`，所以即使其中有一些字符可以被解析为标签（无论合法还是不合法），也应该将它们视为**常规字符**。

**合法代码的例子:**

```
输入: "<DIV>This is the first line <![CDATA[<div>]]></DIV>"

输出: True

解释: 

代码被包含在了闭合的标签内： <DIV> 和 </DIV> 。

TAG_NAME 是合法的，TAG_CONTENT 包含了一些字符和 cdata 。 

即使 CDATA_CONTENT 含有不匹配的起始标签和不合法的 TAG_NAME，它应该被视为普通的文本，而不是标签。

所以 TAG_CONTENT 是合法的，因此代码是合法的。最终返回True。


输入: "<DIV>>>  ![cdata[]] <![CDATA[<div>]>]]>]]>>]</DIV>"

输出: True

解释:

我们首先将代码分割为： start_tag|tag_content|end_tag 。

start_tag -> "<DIV>"

end_tag -> "</DIV>"

tag_content 也可被分割为： text1|cdata|text2 。

text1 -> ">>  ![cdata[]] "

cdata -> "<![CDATA[<div>]>]]>" ，其中 CDATA_CONTENT 为 "<div>]>"

text2 -> "]]>>]"

start_tag 不是 "<DIV>>>" 的原因参照规则 6 。
cdata 不是 "<![CDATA[<div>]>]]>]]>" 的原因参照规则 7 。
```

**不合法代码的例子:**

```
输入: "<A>  <B> </A>   </B>"
输出: False
解释: 不合法。如果 "<A>" 是闭合的，那么 "<B>" 一定是不匹配的，反之亦然。

输入: "<DIV>  div tag is not closed  <DIV>"
输出: False

输入: "<DIV>  unmatched <  </DIV>"
输出: False

输入: "<DIV> closed tags with invalid tag name  <b>123</b> </DIV>"
输出: False

输入: "<DIV> unmatched tags with invalid tag name  </1234567890> and <CDATA[[]]>  </DIV>"
输出: False

输入: "<DIV>  unmatched start tag <B>  and unmatched end tag </C>  </DIV>"
输出: False
```

**注意:**

1. 为简明起见，你可以假设输入的代码（包括提到的**任意字符**）只包含`数字`, 字母, `'<'`,`'>'`,`'/'`,`'!'`,`'['`,`']'`和`' '`。

# **题解**

从代码的起始位置遍历整个代码片段。

- 当我们发现 `<` 时，如果我们目前不在 `cdata` 的范围内，那么我们必须解析这个 `<`，即接下来一定是一个标签（起始标签或结束标签）或者一段 `cdata`。
  - 如果 `<` 后面接着的是 `!`，那么后面一定是一段 `cdata`，接下来必须匹配到 `[CDATA[`。在这之后，我们就可以遍历代码片段直到遇到 `]]>`，表示 cdata 的结束，这中间的所有特殊符号我们都不需要解析。
  - 如果 `<` 后面接着的不是 `!`，那么它一定是一个标签。如果是 `</` 那么它是结束标签，否则是开始标签。
- 我们继续遍历代码片段，直到遇到 `>` 表示标签的结束为止。此时 `<` 或 `</` 与 `>` 之间的部分就是 `TAG_NAME`，我们需要检查 `TAG_NAME` 的合法性。
  - 如果它是一个起始标签，我们会把 `TAG_NAME` 入栈，如果它是一个结束标签，我们需要检查 `TAG_NAME` 和栈顶的元素是否相同。如果不相同或者栈为空，那么这就是一个不合法的结束标签。
- 在代码片段遍历结束后，我们还需要检查两点：
  - 第一是栈是否为空，如果不为空，说明还有未闭合的标签；
  - 第二是代码片段是否被合法的闭合标签包围，我们需要保证在第一个起始标签被闭合后，接下来不会有任何代码，并且每个 `cdata` 必须在栈不为空的时候才能出现。

```java
    Deque<String> stack = new LinkedList<>();
    boolean contains_tag = false;

    public boolean isValidTagName(String s, boolean ending) {
        if (s.length() < 1 || s.length() > 9)
            return false;
        // TAG_NAME 是否是大写
        for (int i = 0; i < s.length(); i++) {
            if (!Character.isUpperCase(s.charAt(i)))
                return false;
        }
        // 结束标签
        if (ending) {
            // 检查 TAG_NAME 和栈顶的元素是否相同
            if (!stack.isEmpty() && stack.peek().equals(s))
                stack.pop();
            else
                return false;
        } else {
            // 开始标签
            contains_tag = true;
            stack.push(s);
        }
        return true;
    }

    public boolean isValidCdata(String s) {
        // 判断开头必须是 [CDATA[
        return s.indexOf("[CDATA[") == 0;
    }

    public boolean isValid591(String code) {
        if (code.charAt(0) != '<' || code.charAt(code.length() - 1) != '>')
            return false;
        // 遍历整个代码片段
        for (int i = 0; i < code.length(); i++) {
            // 是否是结束标签
            boolean ending = false;
            int closeindex;
            // 在第一个起始标签被闭合后，接下来不会有任何代码
            if (stack.isEmpty() && contains_tag)
                return false;
            // 发现 <
            if (code.charAt(i) == '<') {
                // 是否是 <!
                if (!stack.isEmpty() && code.charAt(i + 1) == '!') {
                    // 查询 <! 到 ]]> 直接的长度
                    closeindex = code.indexOf("]]>", i + 1);
                    if (closeindex < 0 || !isValidCdata(code.substring(i + 2, closeindex)))
                        return false;
                } else {
                    if (code.charAt(i + 1) == '/') {
                        i++;
                        // 结束标签
                        ending = true;
                    }
                    // TAG_NAME 长度
                    closeindex = code.indexOf('>', i + 1);
                    if (closeindex < 0 || !isValidTagName(code.substring(i + 1, closeindex), ending))
                        return false;
                }
                i = closeindex;
            }
        }
        return stack.isEmpty() && contains_tag;
    }
```

**复杂度分析**

- 时间复杂度：O(N)，其中 N 是代码片段的长度。
- 空间复杂度：O(N)。