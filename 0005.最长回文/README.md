## 题目

给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 `1000`。

**示例1:**

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

**示例2:**

```
输入: "cbbd"
输出: "bb"
```

## 解题

### 方法  1 ：暴力循环匹配 （Brute Force）💪💪

根据回文子串的定义，列举所有长度大于等于 2 的子串，依次判断它们是否是回文；

暴力解法时间复杂度高，但是思路清晰、编写简单。由于编写正确性的可能性很大，**可以使用暴力匹配算法检验我们编写的其它算法是否正确。**优化的解法在很多时候，是基于“暴力解法”，以空间换时间得到的，因此思考清楚暴力解法，分析其缺点，很多时候能为我们打开思路。

将所有字符范围遍历一遍，判断是否是回文。

```java
    public String longestPalindrome(String s) {
        int len = s.length();
        if (len < 2) {
            return s;
        }

        int maxLen = 1;
        int begin = 0;
        // s.charAt(i) 每次都会检查数组下标越界，因此先转换成字符数组
        char[] charArray = s.toCharArray();

        // 列举所有长度大于 1 的子串 charArray[i..j]
        for (int i = 0; i < len - 1; i++) {
            for (int j = i + 1; j < len; j++) {
                if (j - i + 1 > maxLen && validPalindromic(charArray, i, j)) {
                    maxLen = j - i + 1;
                    begin = i;
                }
            }
        }
        return s.substring(begin, begin + maxLen);
    }

    /**
     * 验证子串 s[left..right] 是否为回文串
     */
    private boolean validPalindromic(char[] charArray, int left, int right) {
        while (left < right) {
            if (charArray[left] != charArray[right]) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
```

复杂度分析：

时间复杂度：*O*(N^3) 里 `N` 是字符串的长度，枚举字符串的左边界、右边界，然后继续验证子串是否是回文子串，这三种操作都与 `N` 相关；
空间复杂度：*O*(1)，只使用到常数个临时变量，与字符串长度无关。

### 方法 2 ：中心扩展算法

确认中间的字符，向两边字符扩充查看是否是回文。

```java
    public String longestPalindrome(String s) {
        if (s == null || s.length() < 1) return "";
        int start = 0, end = 0;
        for (int i = 0; i < s.length(); i++) {
            //中间有单个字符的回文 比如 aba
            int len1 = expandAroundCenter(s, i, i);
            //中间本身就是回文的情况 比如 abba
            int len2 = expandAroundCenter(s, i, i + 1);
            int len = Math.max(len1, len2);
            if (len > end - start) {
                start = i - (len - 1) / 2;
                end = i + len / 2;
            }
        }
        return s.substring(start, end + 1);
    }

    private int expandAroundCenter(String s, int left, int right) {
        int L = left, R = right;
        while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
            L--;
            R++;
        }
        return R - L - 1;
    }
```

复杂度分析

时间复杂度：*O*(n^2)，其中 `n` 是字符串的长度。长度为 `1` 和 `2` 的回文中心分别有 `n` 和 `n−1` 个，每个回文中心最多会向外扩展 *O*(n) 次。

空间复杂度：O(1)。

### 方法 3 ：动态规划

暴力法优化，同样是遍历所有字符范围，但是在判断回文的时候做了优化，没有使用 `while` 循环，而是判断两边字符相同的情况下，如果当前字符长度 > 3，再判断里面的内容是否是回文（里面的字符如果是回文会提前用 `T` 标记），来判断当前字符是否是回文。

```java
0  1  2  3  4  5
a  b  c  c  b  a
↑     ↑
S     E
```

这里使用双重 `for` 循环表示两个指针，每次固定 `E(end)` 指针，然后遍历 `E` 指针之前的所有的字符，判断是否有相等的。

这里就会先找到最小的回文。具体操作如下。

```java
    0  1  2  3  4  5
 0  T  F  F  F  F  T
 1     T  F  F  T  F
 2        T  T  F  F
 3           T  F  F
 4              T  F
 5                 T
```

为了先找到最小回文，这里遍历二维数组是需要有一定的顺序。这里只展示一种**左上到右下的**。

首先判断 `dp[0][1]` ，判断字符串第 `0` 位和第 `1` 位是否相等。相等就改为 `T` 否者就是 `F` 。
然后从左往右按列遍历数组。 `dp[0][1]`  `dp[0][2]`  `dp[1][1]`  `dp[0][3]`  `dp[1][3]` ….

这里到 `dp[2][3]`  的时候结果为 `T`，（`T` 表示两个下标所对应的字符相等）由于只有两个字符，所有不做后续处理。
但是到了  `dp[1][4]` 是判断结果也为 `T` ，字符长度 > 3 ，就要判断字符下标 `1 和 4` 之间的字符是否是回文。`dp[1+1][4-1]` 这个下标记录了`1 和 4` 之间的字符是否是回文，很显然结果是 `T`。
`dp[0][5]` 同理，判断 `dp[1][4]` 是否是 `T`。

```java
    public String longestPalindrome(String s) {
        int len = s.length();
        if (len < 2) {
            return s;
        }

        int maxLen = 1;
        int begin = 0;

        // dp[start][end] 表示 s[start, end] 是否是回文串
        boolean[][] dp = new boolean[len][len];
        char[] charArray = s.toCharArray();

        for (int i = 0; i < len; i++) {
            dp[i][i] = true;
        }
        for (int end = 1; end < len; end++) {
            for (int start = 0; start < end; start++) {
                //判断两边是否相等
                if (charArray[start] != charArray[end]) {
                    dp[start][end] = false;
                } else {
                    //当前字符范围 > 3 才判断字符直接的内容是否是回文
                    if (end - start < 3) {
                        dp[start][end] = true;
                    } else {
                        //dp[start + 1][end - 1] 表示 s[start..end] 之间的内容是否是回文。
                        //这里相当于判断 charArray[start] == charArray[end] && dp[start + 1][end - 1] == true
                        dp[start][end] = dp[start + 1][end - 1];
                    }
                }

                // 只要 dp[start][end] == true 成立，就表示子串 s[start..end] 是回文，此时记录回文长度和起始位置
                if (dp[start][end] && end - start + 1 > maxLen) {
                    maxLen = end - start + 1;
                    begin = start;
                }
            }
        }
        return s.substring(begin, begin + maxLen);
    }
```

复杂度分析

时间复杂度：*O*(n^2)，其中 `n` 是字符串的长度。动态规划的状态总数为 *O*(n^2)，对于每个状态，我们需要转移的时间为 *O*(1)。

空间复杂度：*O*(n^2)，即存储动态规划状态需要的空间。

### 方法 4 ：马拉车（Manacher）算法

中心扩展算法优化，优化了奇偶两种情况，优化了 `while` 循环的次数。

举一个 `mirror` 的例子。

```java
0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18
#  a  #  b  #  c  #  b  #  e  #  b  #  c  #  b  #  e  #
      ↑        ↑           ↑           ↑        ↑
    start      m		       c           i       max
```

`m(mirror)` `c(center)` `max(maxRight)`

当前情况

就是循环 `i` 经过第 `5` 位时，将 `p[5]` 赋值为 `3` ，证明下标 `5` 是左右各有 `3` 个字符的回文。
下标 `i` 经过第 `9` 位 `e` 时，将 `max` 指向下标 `16` 形成最长回文 `2 ~ 16` 。
然后 `i` 继续循环，走到下标 `13` 的时候，当前 `i < max` 
计算 `m` 的位置为下标 `5` ，`p[5]` 内的值为 `3`，又因为 `i` 处于最长回文内，所有 `i` 的前三后三位字符，和下标 `m` 是一样的，可以不用再去循环。

```java
    public String longestPalindrome4(String s) {
        int len = s.length();
        if (len < 2) {
            return s;
        }

        // 得到预处理字符串
        String str = addBoundaries(s, '#');
        // 新字符串的长度
        int sLen = 2 * len + 1;

        // 数组 p 记录了扫描过的回文子串的信息
        int[] p = new int[sLen];

        // 双指针，它们是一一对应的，须同时更新
        int maxRight = 0;
        int center = 0;

        // 当前遍历的中心最大扩散步数，其值等于原始字符串的最长回文子串的长度
        int maxLen = 1;
        // 原始字符串的最长回文子串的起始位置，与 maxLen 必须同时更新
        int start = 0;

        for (int i = 0; i < sLen; i++) {
            if (i < maxRight) {
                //能走到这里，证明现在 最长回文的下标 是大于 循环下标 i 的。
                //当前下标 i 处于 回文内部。
                int mirror = 2 * center - i;
                // mirror 的作用是查看 i 关于 center 所对称的下标(也在回文内部)
                // 是否也存在回文 p[mirror] 记录了  第 mirror 位字符串的回文长度
                // mirror 的回文长度对应了当前循环下标 i 的最小回文长度，可以让当前 while 循环少循环几次
                p[i] = Math.min(maxRight - i, p[mirror]);
            }

            // 下一次尝试扩散的左右起点，能扩散的步数直接加到 p[i] 中
            int left = i - (1 + p[i]);
            int right = i + (1 + p[i]);

            // left >= 0 && right < sLen 保证不越界
            // str.charAt(left) == str.charAt(right) 表示可以扩散 1 次
            while (left >= 0 && right < sLen && str.charAt(left) == str.charAt(right)) {
                p[i]++;
                left--;
                right++;

            }
            // 根据 maxRight 的定义，它是遍历过的 i 的 i + p[i] 的最大者
            // 如果 maxRight 的值越大，进入上面 i < maxRight 的判断的可能性就越大，这样就可以重复利用之前判断过的回文信息了
            if (i + p[i] > maxRight) {
                // maxRight 和 center 需要同时更新
                maxRight = i + p[i];
                center = i;
            }
            if (p[i] > maxLen) {
                // 记录最长回文子串的长度和相应它在原始字符串中的起点
                maxLen = p[i];
                start = (i - maxLen) / 2;
            }
        }
        return s.substring(start, start + maxLen);
    }


    /**
     * 创建预处理字符串
     *
     * @param s      原始字符串
     * @param divide 分隔字符
     * @return 使用分隔字符处理以后得到的字符串
     */
    private String addBoundaries(String s, char divide) {
        int len = s.length();
        if (len == 0) {
            return "";
        }
        if (s.indexOf(divide) != -1) {
            throw new IllegalArgumentException("参数错误，您传递的分割字符，在输入字符串中存在！");
        }
        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < len; i++) {
            stringBuilder.append(divide);
            stringBuilder.append(s.charAt(i));
        }
        stringBuilder.append(divide);
        return stringBuilder.toString();
    }
```

复杂度分析

时间复杂度：*O*(n)，其中 n 是字符串的长度。由于对于每个位置，扩展要么从当前的最右侧臂长 `right` 开始，要么只会进行一步，而 `right` 最多向前走 *O*(n) 步，因此算法的复杂度为 *O*(n)。

空间复杂度：*O*(n)，我们需要 *O*(n) 的空间记录每个位置的臂长。

