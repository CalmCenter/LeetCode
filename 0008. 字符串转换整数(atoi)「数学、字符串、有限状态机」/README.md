## 题目

请你来实现一个 `atoi ` 函数，使其能将字符串转换成整数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。接下来的转化规则如下：

- 如果第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字字符组合起来，形成一个有符号整数。
- 假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成一个整数。
- 该字符串在有效的整数部分之后也可能会存在多余的字符，那么这些字符可以被忽略，它们对函数不应该造成影响。
  注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换，即无法进行有效转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0 。

提示：

- 本题中的空白字符只包括空格字符 ' ' 。
- 假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231,  231 − 1]。如果数值超过这个范围，请返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。

**示例1:**

```
输入: "42"
输出: 42
```

**示例2:**

```
输入: "   -42"
输出: -42
解释: 第一个非空白字符为 '-', 它是一个负号。
     我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
```

**示例3:**

```
输入: "4193 with words"
输出: 4193
解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。
```

**示例4:**

```
输入: "words and 987"
输出: 0
解释: 第一个非空字符是 'w', 但它不是数字或正、负号。
     因此无法执行有效的转换。
```

**示例5:**

```
输入: "-91283472332"
输出: -2147483648
解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 
     因此返回 INT_MIN (−231) 。
```

## 解题

### 方法  1 ：暴力解法

```java
    public int myAtoi(String str) {
        int i = 0, n = str.length();
        //全是空格没有有效字符
        while (i < n && str.charAt(i) == ' ') i++;
        if (i == n) return 0;

        //flag 为 1 表示正数   flag 为 -1 表示负数
        int flag = 1;
        if (str.charAt(i) == '+' || str.charAt(i) == '-') {
            if (str.charAt(i) == '-') flag = -1;
            i++;
        }

        //判断是否是数字
        int ans = 0;
        while (i < n && Character.isDigit(str.charAt(i))) {
            int temp = str.charAt(i) - '0';
            //正数情况，判断最大值
            if (flag == 1 && (ans > Integer.MAX_VALUE / 10 || (ans == Integer.MAX_VALUE / 10 && temp > 7)))
                return Integer.MAX_VALUE;
            //负数情况，判断最小值
            if (flag == -1 && (ans > -(Integer.MIN_VALUE / 10) || (ans == -(Integer.MIN_VALUE / 10) && temp > 8)))
                return Integer.MIN_VALUE;
            ans = ans * 10 + temp;
            i++;
        }
        return ans * flag;
    }
```

复杂度分析

时间复杂度：O(n)

空间复杂度：O(1)

### 方法  2 ：有限状态机

重点在于二维数组控制了 `state` 的值，通过不同的状态省去的很多 `if else` 。

二维数组第一位表示当前状态，第二位表示新字符的状态，然后取出新的状态复制到当前状态中。

重点就是在这个状态赋值和判断状态这儿了。

状态 `0` 不做处理，状态 `1` 判断正负符号，状态 `2` 需要计入结果中，状态 `3` 之后不管出现上面字符都不处理。

```java
public int myAtoi2(String str) {
        Automata auto=new Automata();
        char[] temp=str.toCharArray();
        for(char c:temp)
            auto.get(c);

        return auto.sign*(int)auto.ans;
    }

    class Automata {
        //0 表示空字符不做处理继续遍历
        //1 正负符号 需要修改 sign
        //2 数字 需要推入 ans 中
        //3 非数字字符 中断推入操作返回 ans * sign
        private int state = 0;
        //二维数组里是 state 可能的值
        private int[][] table = {{0, 1, 2, 3}, {3, 3, 2, 3}, {3, 3, 2, 3}, {3, 3, 3, 3}};
        long ans = 0;
        //1 为正数 -1 为负数
        int sign = 1;

        public int gets(char c) {
            if (c == ' ') return 0;
            if (c == '+' || c == '-') return 1;
            if (Character.isDigit(c)) return 2;
            return 3;
        }

        public void get(char c) {
            state = table[state][gets(c)];
            if (state == 2) {
                ans = ans * 10 + (c - '0');
                ans = sign == 1 ? Math.min(ans, Integer.MAX_VALUE) : Math.min(ans, -(long) Integer.MIN_VALUE);
            }
            if (state == 1 && c == '-') sign = -1;
        }
    }
```

复杂度分析

时间复杂度：O(n)，其中n 为字符串的长度。我们只需要依次处理所有的字符，处理每个字符需要的时间为 O(1)。

空间复杂度：O(1)，自动机的状态只需要常数空间存储。

