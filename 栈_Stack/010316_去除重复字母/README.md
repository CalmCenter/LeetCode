给你一个字符串 `s` ，请你去除字符串中重复的字母，使得每个字母只出现一次。需保证 **返回结果的字典序最小**（要求不能打乱其他字符的相对位置）。

注意：该题与 [1081 不同字符的最小子序列](https://leetcode-cn.com/problems/smallest-subsequence-of-distinct-characters) 相同

**示例 1：**

```
输入：s = "bcabc"
输出："abc"
```

**示例 2：**

```
输入：s = "cbacdcbc"
输出："acdb"
```

**提示：**

- `1 <= s.length <= 104`
- `s` 由小写英文字母组成

## 方法一：贪心 + 单调栈

字典序：就是两个单词按每个字符的 `ascii` 码比大小，比如 `abc` 小于 `abd(第三位大)` 或 `acd(第二位大)` 或 `bcd(第一位大)`

思路：

1. 如果一个字符只出现一次，这个字符必须被选取
   如果当前字符出现不是一次，并且后面的字符小于当前字符，则删除当前字符。
2. 按字母升序排序的字典序肯定是最小的

```java
    public String removeDuplicateLetters(String s) {
        boolean[] vis = new boolean[26];
        int[] num = new int[26];
        // s.charAt(i) - 'a' 将小写字母的 ascii 控制在 0 - 25，数组中每一个下标表示 一个字母，值表示字符串中有多少该字母
        for (int i = 0; i < s.length(); i++) {
            num[s.charAt(i) - 'a']++;
        }
        // 结果字符
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            // 没有添加到 结果(sb) 中
            if (!vis[ch - 'a']) {
                // 结果(sb) 中已经有字符，并且，结果(sb) 中的最后一位 ascii > 当前字符，说明 结果(sb) 的 字符串字典序 不是最小。
                while (sb.length() > 0 && sb.charAt(sb.length() - 1) > ch) {
                    // 结果(sb) 中的最后一位的数量不为 0，证明 传入字符串 后面还有相同的字符，之后还可以再添加上
                    if (num[sb.charAt(sb.length() - 1) - 'a'] > 0) {
                        // 去掉 结果(sb) 中的最后一个字符，并改变 vis 状态
                        vis[sb.charAt(sb.length() - 1) - 'a'] = false;
                        sb.deleteCharAt(sb.length() - 1);
                    } else {
                        break;
                    }
                }
                // 将 当前字符 添加到 结果，并改变 vis 状态
                vis[ch - 'a'] = true;
                sb.append(ch);
            }
            // 当前字符的数量 -1
            num[ch - 'a'] -= 1;
        }
        return sb.toString();
    }
```

**复杂度分析**

- 时间复杂度：O(N)，其中 N 为字符串长度。代码中虽然有双重循环，但是每个字符至多只会入栈、出栈各一次。

- 空间复杂度：O(∣Σ∣)，其中 Σ 为字符集合，本题中字符均为小写字母，所以 ∣Σ∣=26。由于栈中的字符不能重复，因此栈中最多只能有 ∣Σ∣ 个字符，另外需要维护两个数组，分别记录每个字符是否出现在栈中以及每个字符的剩余数量。

  