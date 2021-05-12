一个数组的 **最小乘积** 定义为这个数组中 **最小值** **乘以** 数组的 **和** 。

- 比方说，数组 `[3,2,5]` （最小值是 `2`）的最小乘积为 `2 * (3+2+5) = 2 * 10 = 20` 。

给你一个正整数数组 `nums` ，请你返回 `nums` 任意 **非空子数组** 的**最小乘积** 的 **最大值** 。由于答案可能很大，请你返回答案对 `10^9 + 7` **取余** 的结果。

请注意，最小乘积的最大值考虑的是取余操作 **之前** 的结果。题目保证最小乘积的最大值在 **不取余** 的情况下可以用 **64 位有符号整数** 保存。

**子数组** 定义为一个数组的 **连续** 部分。

 **示例 1：**

```
输入：nums = [1,2,3,2]
输出：14
解释：最小乘积的最大值由子数组 [2,3,2] （最小值是 2）得到。
2 * (2+3+2) = 2 * 7 = 14 。
```

**示例 2：**

```
输入：nums = [2,3,3,1,2]
输出：18
解释：最小乘积的最大值由子数组 [3,3] （最小值是 3）得到。
3 * (3+3) = 3 * 6 = 18 。
```

**示例 3：**

```
输入：nums = [3,1,5,6,4,2]
输出：60
解释：最小乘积的最大值由子数组 [5,6,4] （最小值是 4）得到。
4 * (5+6+4) = 4 * 15 = 60 。
```

##  前缀和+单调栈

读题知：是一段连续的子数组，取子数组中的最小值 * 子数组和 = 一个值，这个值是所有可操作子数组中的最大值。

思路就是，设当前 `nums[i]` 为子数组中的最小值。然后寻找当前下标 左右 第一个比  `nums[i]`   小的值，两边小值中间的元素，就形成一个最小值为  `nums[i]` 的子数组。这样计算出的值就是当前 `nums[i]` 可求得最大结果 `（nums[i] * 子数组和）`。

```java
    public int maxSumMinProduct(int[] nums) {
        // 数组前缀和
        long[] pre = new long[nums.length];  // 此处要用long型数组，否则会越界
        pre[0] = nums[0];
        for (int i = 1; i < nums.length; i++) {
            pre[i] = pre[i - 1] + nums[i];
        }

        // 单递增调栈
        Deque<Integer> stack = new LinkedList<>();
        // 求元素右边第一个比其小的
        int[] rightLower = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            // 单调递增栈  记录每个值右边第一个比自己小的下标，如果没有就证明自己是最小的或者是边界
            while (!stack.isEmpty() && nums[stack.peek()] > nums[i]) {
                int t = stack.pop();
                rightLower[t] = i;
            }
            stack.push(i);
        }
        while (!stack.isEmpty()) {
            int t = stack.pop();
            // 数组前后可以加上哨兵，前后都加一个 0；否则此处会出现越界情况，后续需要加特殊判断
            rightLower[t] = nums.length;
        }
        // 求元素左边第一个比其小的
        int[] leftLower = new int[nums.length];
        for (int i = nums.length - 1; i >= 0; i--) {
            // 记录每个值左边边第一个比自己小的下标，如果没有就证明自己是最小的或者是边界
            while (!stack.isEmpty() && nums[stack.peek()] > nums[i]) {
                int t = stack.pop();
                leftLower[t] = i;
            }
            stack.push(i);
        }
        while (!stack.isEmpty()) {
            int t = stack.pop();
            leftLower[t] = -1;
        }

        // 在前缀和及单调栈基础上，求最终解
        long ans = 0;
        for (int i = 0; i < nums.length; i++) {
            int r = rightLower[i];
            int l = leftLower[i];
            long t;
            if (r == nums.length && l == -1) {// 所有数据最小的
                t = pre[nums.length - 1];
            } else if (r == nums.length) {  // 越界情况特殊判断  证明 i 的右边没有比 nums[i] 更小的
                t = pre[nums.length - 1] - pre[l];
            } else if (l == -1) {  // 越界情况特殊判断 证明 i 的左边没有比 nums[i] 更小的
                t = pre[r] - nums[r];
            } else {
                // pre[4] - pre[1] - nums[4] 意思就是计算 nums[2] + nums[3] 的值
                // 因为 pre[r] 与 pre[l] 都是左右第一个比 num[i] 小的下标
                // 所以计算其中间的和，就是 连续节点中 和最大 并且 最小值为 nums[i] 的子数组 。
                // 题目中子数组 定义为一个数组的 连续 部分。
                t = pre[r] - pre[l] - nums[r];
            }
            ans = Math.max(ans, t * nums[i]);
        }
        return (int) (ans % 1000000007);
    }
```

复杂度分析

时间复杂度 : O(n)。 

空间复杂度 : O(n)。