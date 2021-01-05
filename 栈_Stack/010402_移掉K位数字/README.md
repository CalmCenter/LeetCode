给定一个以字符串表示的非负整数 `num`，移除这个数中的 `k` 位数字，使得剩下的数字最小。

**注意:**

- `num` 的长度小于 `10002` 且 `≥ k`。
- `num` 不会包含任何前导零。

**示例 1 :**

```
输入: num = "1432219", k = 3
输出: "1219"
解释: 移除掉三个数字 4, 3, 和 2 形成一个新的最小的数字 1219。
```

**示例 2 :**

```
输入: num = "10200", k = 1
输出: "200"
解释: 移掉首位的 1 剩下的数字为 200. 注意输出不能有任何前导零。
```

示例 **3 :**

```
输入: num = "10", k = 2
输出: "0"
解释: 从原数字移除所有的数字，剩余为空就是0。
```

## 题解 

1. 尽可能的将高位（左边）的数最小
2. 如果从左到右已经从大到小了，k 不为 0 ，则需要删除后几位大值
3. 将栈转化为字符串，需要注意高位是 0 的情况

```java
   public String removeKdigits(String num, int k) {
        Deque<Character> deque = new LinkedList<Character>();
        int length = num.length();
        for (int i = 0; i < length; ++i) {
            char digit = num.charAt(i);
            // 高位 与之前位相比较 尽可能小
            while (!deque.isEmpty() && k > 0 && deque.peekLast() > digit) {
                deque.pollLast();
                k--;
            }
            deque.offerLast(digit);
        }

        // 循环结束如果 k != 0 说明 循环结果从左到右 从小到大
        // 需要把后 k 位 最大的数 删除
        for (int i = 0; i < k; ++i) {
            deque.pollLast();
        }

        // 将栈中的元素赋值到 String 中
        StringBuilder ret = new StringBuilder();
        boolean leadingZero = true;
        while (!deque.isEmpty()) {
            char digit = deque.pollFirst();
            // 如果前几位 存在 0 则跳过 
            if (leadingZero && digit == '0') {
                continue;
            }
            leadingZero = false;
            ret.append(digit);
        }
        return ret.length() == 0 ? "0" : ret.toString();
    }
```

**复杂度分析**

时间复杂度：O(n)，其中 n 为字符串的长度。尽管存在嵌套循环，但内部循环最多运行 k 次。由于 0<k≤n，主循环的时间复杂度被限制在 2n 以内。对于主循环之外的逻辑，它们的时间复杂度是 O(n)，因此总时间复杂度为 O(n)。

空间复杂度：O(n)。栈存储数字需要线性的空间。
