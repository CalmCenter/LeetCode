实现一个基本的计算器来计算一个简单的字符串表达式的值。

字符串表达式仅包含非负整数，`+`， `-` ，`*`，`/` 四种运算符和空格 ` `。 整数除法仅保留整数部分。

**示例 1:**

```
输入: "3+2*2"
输出: 7
```

**示例 2:**

```
输入: " 3/2 "
输出: 1
```

**示例 3:**

```
输入: " 3+5 / 2 "
输出: 5
```

**说明：**

- 你可以假设所给定的表达式都是有效的。
- 请**不要**使用内置的库函数 `eval`。

# 解法

先计算乘除，加减保存下来最后算

```java
    public int calculate(String s) {
        char[] chs = s.toCharArray();
        Deque<Integer> stack = new LinkedList<>();
        int operand = 0;
        // 记录数值计算方式
        char operation = '+';
        for (int i = 0; i < chs.length; i++) {
            if (chs[i] == ' ') {
                continue;
            }
            if (Character.isDigit(chs[i])) {
                // 可能是多位数，计算这个数值
                operand = operand * 10 + (chs[i] - '0');
            }
            // 如果是最后一位，也需要进行计算，但可以不做记录
            if (!Character.isDigit(chs[i]) || i >= chs.length - 1) {
                // 先计算乘除，加减只做保存
                if (operation == '+') {
                    stack.push(operand);
                } else if (operation == '-') {
                    stack.push(-operand);
                } else if (operation == '*') {
                    int a = stack.pop();
                    stack.push(a * operand);
                } else if (operation == '/') {
                    int a = stack.pop();
                    stack.push(a / operand);
                }
                // 最后一位可以不做记录
                if (i >= chs.length - 1) {
                    break;
                }
                // 保存当前符号
                operation = chs[i];
                operand = 0;
            }
        }

        int sum = 0;
        // 乘除计算完后，再计算加减
        while (!stack.isEmpty()) {
            sum += stack.pop();
        }
        return sum;
    }
```

**复杂度分析**

- 时间复杂度：O(N)，其中 N 指的是字符串的长度。
- 空间复杂度：O(N)