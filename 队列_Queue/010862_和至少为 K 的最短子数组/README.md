返回 `A` 的最短的非空连续子数组的**长度**，该子数组的和至少为 `K` 。

如果没有和至少为 `K` 的非空子数组，返回 `-1` 。

**示例 1：**

```
输入：A = [1], K = 1
输出：1
```

**示例 2：**

```
输入：A = [1,2], K = 4
输出：-1
```

**示例 3：**

```
输入：A = [2,-1,2], K = 3
输出：3
```

**提示：**

1. `1 <= A.length <= 50000`
2. `-10 ^ 5 <= A[i] <= 10 ^ 5`
3. `1 <= K <= 10 ^ 9`

# 题解一 暴力

```java
    public int shortestSubarray(int[] A, int K) {
        int minLen = A.length + 1;
        for (int i = 0; i < A.length; i++) {
            for (int j = i; j < A.length; j++) {
                int sum = 0;
                // 将下标为 i 至 j 的元素相加
                for (int k = i; k <= j; k++) {
                    sum += A[k];
                }
                // 结果如果 >= k 并且 两个下标之间的长度更短
                if (sum >= K && (j - i + 1) < minLen) {
                    minLen = j - i + 1;
                }
            }
        }
        // 如果 长度没有变化 则为 -1 
        return minLen == A.length + 1 ? -1 : minLen;
    }
```

**复杂度分析**

时间复杂度：O(n^3)

空间复杂度：O(1)

# 题解二 动态规划优化暴力

暴力解法中，我们可以看到，它之所以暴力，是因为它的很多计算都是重复的，比如在最内层的循环中求和，前`k`个元素的和在求前`k+1`，`k+2` ... `k+n` 个元素的和时又重复计算了一便，这种有重叠子问题的方法，是典型的可以用 [动态规划](https://zh.wikipedia.org/wiki/动态规划)([Dynamic programming](https://en.wikipedia.org/wiki/Dynamic_programming)) 的思想来解决的，这个重复计算的过程是可以省略的，前`k+1`个元素的和就是前`k`个元素的和再加第`k+1`个元素就可以了。

基于这样的想法，我们可以考虑先构造这样一个数组 `preSum`，它的第 `i` 个元素 `preSum[i]` 表示输入数组 `A` 的前 `i `个元素（`0`到`i-1`，不包含`i`）的和，即

```
preSum[i] = A[0] + A[1] + ... + A[i-1]
```

这里有一个边界条件的处理问题，我们把 `preSum[0]` 定义成了 `A[0] `，忽视了 `A` 为空的情况，在处理上面的 示例 1 的时候就遇到了问题 。所以默认 `A[0]` 为 `0` 。

```java
    public int shortestSubarray(int[] A, int K) {
        int minLen = A.length + 1;
        int[] preSum = new int[A.length + 1];
        preSum[0] = 0;
        for (int i = 0; i < A.length; i++) {
            preSum[i + 1] = preSum[i] + A[i];
        }
      
        for (int i = 0; i < A.length; i++) {
            for (int j = i + 1; j < A.length + 1; j++) {
                // preSum[j] - preSum[i] 相当于 i 到 j 之间的和
                if ((preSum[j] - preSum[i]) >= K) {
                    if ((j - i) < minLen) {
                        minLen = j - i;
                    }
                }
            }
        }
        return minLen == A.length + 1 ? -1 : minLen;
    }
```

**复杂度分析**

时间复杂度：O(n^2)

空间复杂度：O(n)

# 题解三 再优化

优化无意义的比较

```java
    public int shortestSubarray(int[] A, int K) {
        int N = A.length;
        long[] sum = new long[N+1];
        for (int i = 0; i < N; ++i)
            sum[i+1] = sum[i] + (long) A[i];

        int ans = N+1;
        Deque<Integer> monoq = new LinkedList();

        for (int i = 0; i < sum.length; ++i) {
            // monoq.getLast() 记为 x , i > x
            // 如果 sum[i] <= sum[x] ,i 位数的和 小于等于 x 位数的和
            // 则证明下标 x 到 i 之间的数存在 负数 或 0
            // 那么当 sum[i+n] - sum[x] >= K ，则必然有 sum[i+n] - sum[i] >= K  注：i+n 表示 i 之后的某个数
            // 因为如果不添加 x 到 i 之间的那几个负数或 0 ，i 到 i+n 之间的和肯定 大于或等于 x 到 i+n 之间的和。
            // 所以就不需要计算 sum[i+n] - sum[x] 了，因为 sum[i+n] - sum[i] 一定是更大的或相等，而且长度也更小。
            while (!monoq.isEmpty() && sum[i] <= sum[monoq.getLast()])
                monoq.removeLast();
            while (!monoq.isEmpty() && sum[i] - sum[monoq.getFirst()] >= K)
                // monoq.getFirst() 记为 x , i > x 
                // 当 sum[i] - sum[x] >= k , x 就可以不需要再比较了,
                // 因为 x 到 i 之间数的和已经满足了 K,再从 x 到 i+1 去记录,已经不会比  x 到 i 长度更短了
                // 所以需要 跳过 x ,再寻找 x 到 i 之间是否存在一个数,到下标 i 的和 >=k ,这样不仅满足 K ,而且长度更短  
                ans = Math.min(ans, i - monoq.removeFirst());

            monoq.addLast(i);
        }

        return ans < N+1 ? ans : -1;
    }
```

**复杂度分析**

- 时间复杂度：O(N)，其中 N 是数组 `A` 的长度。
- 空间复杂度：O(N)。





[和至少为 K 的最短子数组详解](https://github.com/Shellbye/Shellbye.github.io/issues/41)