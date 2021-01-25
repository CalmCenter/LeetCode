给出由小写字母组成的字符串 `S`，**重复项删除操作**会选择两个相邻且相同的字母，并删除它们。

在 S 上反复执行重复项删除操作，直到无法继续删除。

在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

**示例：**

```
输入："abbaca"
输出："ca"
解释：
例如，在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。之后我们得到字符串 "aaca"，其中又只有 "aa" 可以执行重复项删除操作，所以最后的字符串为 "ca"。
```

**提示：**

1. `1 <= S.length <= 20000`
2. `S` 仅由小写英文字母组成。

# 题解

将 `aa` 到 `zz` 的 26 种重复项放入集合中；

遍历这 26 种重复项，并用字符串的替换函数把重复项替换成空串。

在进行过一次替换之后，可能会出现新的重复项。例如对于字符串 `abbaca`，如果替换了重复项 `bb`，字符串会变为 `aaca`，出现了新的重复项 `aa`。因此，上面的过程需要背重复若干次，直到字符串在一整轮替换过程后保持不变（即长度不变）为止。

```java
    public String removeDuplicates(String S) {
        // generate 26 possible duplicates
        HashSet<String> duplicates = new HashSet();
        StringBuilder sb = new StringBuilder();
        // 添加 aa、bb、cc ... zz 到 duplicates
        for (char i = 'a'; i <= 'z'; ++i) {
            sb.setLength(0);
            sb.append(i); sb.append(i);
            duplicates.add(sb.toString());
        }

        int prevLength = -1;
        while (prevLength != S.length()) {
            prevLength = S.length();
            // 在 S 中寻找 duplicates 并替换
            for (String d : duplicates) S = S.replace(d, "");
        }

        return S;
    }
```

**复杂度分析**

- 时间复杂度：O(N^2)，其中 N 是字符串的长度。代码中的过程为 `while -> for -> replace`，其中 `while` 执行的次数最多为 N/2，`for` 固定执行 `26 `次，`replace` 的平均复杂度为 O(N)，因此总的时间复杂度为 O(N^2)
- 空间复杂度：O(N)。由于字符串的替换函数会生成一份原字符串的拷贝，因此空间复杂度为 O(N)。

# 题解二 栈

- 若当前的字母和栈顶的字母相同，则弹出栈顶的字母；
- 若当前的字母和栈顶的字母不同，则放入当前的字母。

```java
    public String removeDuplicates2(String S) {
        StringBuilder sb = new StringBuilder();
        int sbLength = 0;
        // 循环每一个字符
        for (char character : S.toCharArray()) {
            // 当前字符 如果和 上一个字符相同
            if (sbLength != 0 && character == sb.charAt(sbLength - 1))
                // 删除最后一个字符 并且 length--
                sb.deleteCharAt(sbLength-- - 1);
            else {
                // 如果不相同则添加
                sb.append(character);
                sbLength++;
            }
        }
        return sb.toString();
    }
```

**复杂度分析**

- 时间复杂度：O(N)，其中 N 是字符串的长度。
- 空间复杂度：O(N)。