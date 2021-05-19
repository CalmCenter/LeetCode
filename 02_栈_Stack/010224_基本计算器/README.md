实现一个基本的计算器来计算一个简单的字符串表达式的值。

字符串表达式可以包含左括号 `(` ，右括号 `)`，加号 `+` ，减号 `-`，**非负**整数和空格 ` `。

**示例 1:**

```
输入: "1 + 1"
输出: 2
```

**示例 2:**

```
输入: " 2-1 + 2 "
输出: 3
```

**示例 3:**

```
输入: "(1+(4+5+2)-3)+(6+8)"
输出: 23
```

**说明：**

- 你可以假设所给定的表达式都是有效的。
- 请**不要**使用内置的库函数 `eval`。

# 方法一  栈、反转字符串

操作只有加减法，适合使用栈来解决，因为表达中包含括号，我们可以先处理每个括号中的子表达式，再处理主表达式

我们将表达式挨个添加到栈上，直到遇见 `)` 时，开始逐个弹出栈中的元素，直到 `(` 终止。

但是在弹出元素的时候，元素是倒着输出的，需要明白虽然 加法`(+) `在倒着加是没问题的，但是 减法 却不行，也就是说，如果推入栈的是 `(A-B)` 这个表达式，弹出的表达式将是 `(B-A)` ，这样值就不正确了。

所以为了解决这个问题，我们可以通过反转字符串，然后再添加到栈中，弹出的顺序就正确了。

- （1）按逆序迭代字符串。
- （2）操作数可以由多个字符组成，字符串 `"123"` 表示数字 `123`，它可以被构造为：`123` >> `120 + 3` >> `100 + 20 + 3`。如果我们读取的字符是一个数字，则我们要将读取的数字乘以 `10` 的幂并将当前数字相加，形成操作数。因为我们是按逆序处理字符串。`(代码中用 n 表示第几位数)`
- （3）操作数由多个字符组成，一旦我们遇到的字符不是数字，则我们将操作数添加到栈上。
- （4）当我们遇到最括号 `(`，这意味这遇到了一个子表达式结束。由于我们是逆序，所以左括号成了表达式的结尾。则需要从栈中弹出操作数和运算发来计算表达式，直到弹出相应的右括号。子表达式的最终结果最终添加到栈上。
- （5）将非数字字符添加到栈上。
- （6）最后，可能我们没有更多的字符要处理，但是栈仍然是非空的。当主表达式没有用括号括起来时，就会发生这种情况。因此，在完成对整个表达式求值之后，我们将检查栈是否非空。如果是的话，我们将栈中的元素作为最终表达式处理，并像遇到左括号时那样对其求值。我们还可以用一组括号覆盖原表达式，以此避免额外调用。

```java
    public int calculate(String s) {
        int operand = 0;
        int n = 0;
        Deque<Object> stack = new LinkedList<>();
        //（1）
        for (int i = s.length() - 1; i >= 0; i--) {
            char ch = s.charAt(i);
            if (Character.isDigit(ch)) {
                //（2）
                operand = (int) Math.pow(10, n) * (int) (ch - '0') + operand;
                n += 1;
            } else if (ch != ' ') {
                if (n != 0) {
                    //（3）
                    stack.push(operand);
                    n = 0;
                    operand = 0;
                }
                if (ch == '(') {
                    //（4）
                    int res = evaluateExpr(stack);
                    stack.pop();
                    stack.push(res);
                } else {
                    //（5）
                    stack.push(ch);
                }
            }
        }

        if (n != 0) {
            stack.push(operand);
        }
        //（6）
        return evaluateExpr(stack);
    }

    public int evaluateExpr(Deque<Object> stack) {
        int res = 0;
        if (!stack.isEmpty()) {
            res = (int) stack.pop();
        }
        //（4）
        // 对表达式求值,直到我们得到相应的 ')' 计算 () 之间的值
        while (!stack.isEmpty() && !((char) stack.peek() == ')')) {

            char sign = (char) stack.pop();

            if (sign == '+') {
                res += (int) stack.pop();
            } else {
                res -= (int) stack.pop();
            }
        }
        return res;
    }

```

**复杂度分析**

- 时间复杂度：O(N)，其中 N 指的是字符串的长度。
- 空间复杂度：O(N)

# 方法二 栈和不反转字符串

```java
   public int calculate(String s) {

        Deque<Integer> stack = new LinkedList<>();
        int operand = 0;
        int result = 0; // For the on-going result
        int sign = 1;  // 1 表示 + , -1 表示 -

        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);
            if (Character.isDigit(ch)) {
                // 计算数值，可能是多位数
                operand = 10 * operand + (int) (ch - '0');
            } else if (ch == '+') {
                result += sign * operand;
                sign = 1;
                operand = 0;
            } else if (ch == '-') {
                result += sign * operand;
                sign = -1;
                operand = 0;
            } else if (ch == '(') {
                // 记录括号外的值，以及括号内的值的正负情况
                stack.push(result);
                stack.push(sign);
                // 进入新的 括号，初始化 符号 以及 结果
                sign = 1;
                result = 0;
            } else if (ch == ')') {
                // 计算当前的值 因为 ) 之前肯定是数字，数字只做了存储，没有计算
                result += sign * operand;
                // 计算 括号内 的 正负情况
                result *= stack.pop();
                // 计算 括号外 与 括号内的值 的计算
                result += stack.pop();
                operand = 0;
            }
        }
        return result + (sign * operand);
    }
```

**复杂度分析**

时间复杂度：O(N)，其中 N 指的是字符串的长度。这种方法与前一种方法的区别在于，这种方法的每个字符都将被精确的处理一次。但是前面的方法中，每个字符可能被处理两次，一次是被添加到栈上，另一次是被弹出处理最终结果。这就是为什么这种方法更快的原因。
空间复杂度：O(N)，其中 N 指的是字符串的长度。

