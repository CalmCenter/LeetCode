## 题目

给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

**示例1:**

```
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**示例2:**

```
输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

**示例3:**

```
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

## 解题

以下三种方式思路类似，都可以认为是双指针。

只是指针的行走方式不太一样。

### 方法 1：双指针

别人叫它 **滑动窗口** 。

思路：

`HashSet` 的作用只是为了判断字符是否重复！

`for` 循环的 `i` 表示左指针；

`while`  循环不断移动右指针，`while` 循环结束，证明下一个字符重复，或者循环完毕；

记录当前字符长度 `rk - i + 1` ，右指针 - 左指针 + 1。(比如 `abca` 当 `while` 循环到第二个 `a` 发现重复的时候 `2-0+1`)；

然后 左指针 开始删除左边第一个，直到删除了重复的那个值。

```java
    public int lengthOfLongestSubstring(String s) {
        // 哈希集合，记录每个字符是否出现过，如果存在某个字符，从头(左指针)开始删除，直到不再包含，然后操作右指针。
        Set<Character> occ = new HashSet<>();
        int length = s.length();
        // 右指针，初始值为 -1，相当于我们在字符串的左边界的左侧，还没有开始移动
        int rk = -1, max = 0;
        for (int i = 0; i < length; ++i) {
            if (i != 0) {
                // 左指针向右移动一格，移除一个字符
                occ.remove(s.charAt(i - 1));
            }
            while (rk + 1 < length && !occ.contains(s.charAt(rk + 1))) {
                // 不断地移动右指针
                occ.add(s.charAt(rk + 1));
                ++rk;
            }
            // 第 i 到 rk 个字符是一个无重复字符子串
            max = Math.max(max, rk - i + 1);

//            for (int n = 0; n < i; n++) {
//                System.out.print(s.charAt(n));
//            }
//            System.out.print(" ");
//            for (int n = i; n <= rk; n++) {
//                System.out.print(s.charAt(n));
//            }
//            System.out.print(" ");
//            for (int n = rk + 1; n < length; n++) {
//                System.out.print(s.charAt(n));
//            }
//            System.out.println();

            //如果当前左指针右面的字符长度<当前最长子串，则退出循环。
            //毕竟能走到这就证明 occ 中还存在重复，或者右指针已经走完了。证明剩下的数据已经不可能比当前子串长了。
            if (max >= length - i - 1) {
                return max;
            }
        }
        return max;
    }
```

我的输入数据 `abcbaabcdfgaadebc` 最长的一段 `bcdfga` 

```
 abc baabcdfgaadebc
a bc baabcdfgaadebc
ab cba abcdfgaadebc
abc ba abcdfgaadebc
abcb a abcdfgaadebc
abcba abcdfg aadebc
abcbaa bcdfga adebc
abcbaab cdfga adebc
abcbaabc dfga adebc
abcbaabcd fga adebc
abcbaabcdf ga adebc
```

因为没有办法定位 `a` 的之前数据的位置，所以得一次次循环删除前面的数据，直到 `occ.contains(a)` 通过。
但由于剩下的数据肯定不可能比当前最大值大了，所以提前推出。

**复杂度分析**

- 时间复杂度：O(N)，其中 N 是字符串的长度。左指针和右指针分别会遍历整个字符串一次。

- 空间复杂度：O(∣Σ∣)，其中 Σ 表示字符集（即字符串中可以出现的字符），∣Σ∣ 表示字符集的大小。在本题中没有明确说明字符集，因此可以默认为所有 ASCII 码在 [0,128) 内的字符，即 ∣Σ∣=128。我们需要用到哈希集合来存储出现过的字符，而字符最多有 ∣Σ∣ 个，因此空间复杂度为 O(∣Σ∣)。

### 方法  2：HashMap

思路：

`HashMap` 的作用只是为了判断字符是否重复！

记录最大重复字符下标 `repeatValue`  。( 可能出现 `abcba` 这种情况，`a` 后重复比 `b` 先重复的下标小，所以需要用 `Math.max` )

所以，`repeatValue` 下标对应的值 到 当前循环 `i` 对应的字符，肯定是一串无重复的子串。

每次循环刷新 `max` 值。

```java
    public int lengthOfLongestSubstring(String s) {
        int length = s.length();
      	//最长子串的长度
        int max = 0;
        Map<Character, Integer> map = new HashMap<>();
        //repeatValue 最大的重复字符的下标
        int repeatValue = -1;
        //i 表示右指针
        for (int i = 0; i < length; i++) {
            char element = s.charAt(i);
            if (map.containsKey(element)) {
                repeatValue = Math.max(map.get(element), repeatValue);
            }
            max = Math.max(max, i - repeatValue);

//            for (int n = 0; n < repeatValue+1; n++) {
//                System.out.print(s.charAt(n));
//            }
//            System.out.print(" ");
//            for (int n = repeatValue + 1; n <= i; n++) {
//                System.out.print(s.charAt(n));
//            }
//            System.out.print(" ");
//            for (int n = i+1; n < length; n++) {
//                System.out.print(s.charAt(n));
//            }
//            System.out.println();
          
            //(repeatValue)最大的重复字符下标，右面的字符数量 <= 最大子串数量，则循环没有意义，即使剩下的全部连续不重复也和最大子串一样长
            if (max >= length - repeatValue - 1) {
                return max;
            }
            map.put(element, i);
        }
        return max;
    }
```

### 方法  3：数组

这种方式和方法二思路一样，速度比方法一，方法二都快，但是有数值限制，不可以是中文字符。

将字符对应的 `ASCII` 码，保存在数组下标为 `ASCII` 的位置，值为字符的下标。

出现重复字符时，`dict[ASCII]` 将不再是 `-1` ，如果 `dict[ASCII] > repeatValue` 当前重复的下标 > 记录的最大下标，刷新 `repeatValue` ，重新计算 `maxSize`。

```java
    public int lengthOfLongestSubstring(String s) {
        int maxSize = 0;
        //记录ASCII 码字符出现的位置，以字符作为下标
        int[] dict = new int[128];
        //这里把数组内容全部设为 -1，之后在记录的时候就可以从 0 开始，方便理解
        Arrays.fill(dict, -1);
        //用于记录最大的重复 ASCII 码字符出现的位置的值
        int repeatValue = -1;
        // 当前下标
        int i = 0;
        int ASCII;
        while (i < s.length()) {
            ASCII = s.charAt(i);
            //如果当前位置的值 > repeatValue，证明当前位置已经赋过一次值了，证明字符重复
            if (dict[ASCII] > repeatValue)
                //更新 repeatValue 为之前赋值的下标
                repeatValue = dict[ASCII];
            //将当前下标赋值到数组相应位置
            dict[ASCII] = i;
            //i - repeatValue(去除重复部分)
            // 比如 abcabcdade 中的三个 a 的计算  abca - a(3 - 0)=bca   abcabcda - abca(7 - 3)=bcda
            maxSize = Math.max(maxSize, i - repeatValue);

//            for (int n = 0; n < repeatValue+1; n++) {
//                System.out.print(s.charAt(n));
//            }
//            System.out.print(" ");
//            for (int n = repeatValue + 1; n <= i; n++) {
//                System.out.print(s.charAt(n));
//            }
//            System.out.print(" ");
//            for (int n = i+1; n < s.length(); n++) {
//                System.out.print(s.charAt(n));
//            }
//            System.out.println();

            //s.length() - repeatValue - 1 判断剩下的数有没有必要继续循环
            //(repeatValue)最大的重复字符下标，右面的字符数量 <= 最大子串数量，则循环没有意义，即使剩下的全部连续不重复也和最大子串一样长
            if (maxSize >= s.length() - repeatValue - 1) {
                return maxSize;
            }
          
            i++;
        }
        return maxSize;
    }
```

