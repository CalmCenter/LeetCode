给定一个整数序列：`a1, a2, ..., an`，一个 `132` 模式的子序列 `ai, aj, ak` 被定义为：当 `i < j < k` 时，`ai < ak < aj`。设计一个算法，当给定有 `n` 个数字的序列时，验证这个序列中是否含有 `132` 模式的子序列。

注意：`n` 的值小于 `15000` 。

**示例1:**

```
输入: [1, 2, 3, 4]

输出: False

解释: 序列中不存在132模式的子序列。
```

**示例 2:**

```
输入: [3, 1, 4, 2]

输出: True

解释: 序列中有 1 个132模式的子序列： [1, 4, 2].
```

**示例 3:**

```
输入: [-1, 3, 2, 0]

输出: True

解释: 序列中有 3 个132模式的的子序列: [-1, 3, 2], [-1, 3, 0] 和 [-1, 2, 0].
```

## 题解

```java
    public boolean find132pattern(int[] nums) {
        if (nums.length < 3)
            return false;
        Deque<Integer> stack = new LinkedList<>();
        // 首先记录当前元素 前的 最小元素
        int[] min = new int[nums.length];
        min[0] = nums[0];
        for (int i = 1; i < nums.length; i++)
            min[i] = Math.min(min[i - 1], nums[i]);

        for (int j = nums.length - 1; j >= 0; j--) {
            // 跳过 Min 本身
            if (nums[j] > min[j]) {
                // stack.peek() 由于是倒叙循环，所以表示 后一个元素
                // 当后一个元素 > 当前最小值时  这里表示题目中的(ai < ak)，才能跳过 while
                while (!stack.isEmpty() && stack.peek() <= min[j])
                    stack.pop();
                // 当后一个元素 < 当前元素 这里表示题目中的(ak < aj)，直接返回，证明已经有成立的了
                if (!stack.isEmpty() && stack.peek() < nums[j])
                    return true;
                stack.push(nums[j]);
            }
        }
        return false;
    }
```

**复杂度分析**

- 时间复杂度：O(N)。
- 空间复杂度：O(N)。