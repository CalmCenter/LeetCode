有些数的素因子只有 3，5，7，请设计一个算法找出第 k 个数。注意，不是必须有这些素因子，而是必须不包含其他的素因子。例如，前几个数按顺序应该是 1，3，5，7，9，15，21。

**示例 1:**

```
输入: k = 5

输出: 9
```

# 题解一

```
dp:1
p3:0 , p5:0 , p7:0


i:1
dp[1] = min(1 * 3,1 * 5,1 * 7) = 3
dp: 1 3
p3:1 , p5:0 , p7:0

i:2
dp[2] = min(3 * 3,1 * 5,1 * 7) = 5
dp: 1 3 5
p3:1 , p5:1 , p7:0

i:3
dp[3] = min(3 * 3,3 * 5,1 * 7) = 7
dp: 1 3 5 7
p3:1 , p5:1 , p7:1

i:4
dp[4] = min(3 * 3,3 * 5,3 * 7) = 9
dp: 1 3 5 7 9
p3:2 , p5:1 , p7:1
```



```java
    public int getKthMagicNumber(int k) {
        int p3 = 0, p5 = 0, p7 = 0;
        int[] dp = new int[k];
        dp[0] = 1;
        for (int i = 1; i < k; i++) {
            // 选出最小的数字
            dp[i] = Math.min(dp[p3] * 3, Math.min(dp[p5] * 5, dp[p7] * 7));
            // 将该数字对应的指针向前移动一步。
            if (dp[i] == dp[p3] * 3) p3++;
            if (dp[i] == dp[p5] * 5) p5++;
            if (dp[i] == dp[p7] * 7) p7++;
        }
        return dp[k - 1];
    }
```

 **复杂度分析：**

- **时间复杂度 O(k)**
- **空间复杂度 O(k) **

# 题解二 优先队列

```java
    public int getKthMagicNumber2(int k) {
        // 最小堆处理写入数值  试了Integer不够
        PriorityQueue<Long> PriorityQueue = new PriorityQueue<>();
        // HashSet 保存k个位数值
        Set<Long> list = new HashSet<>();
        PriorityQueue.add(1L);
        while ( true ) {
            // 获取并删除队首元素
            Long val = PriorityQueue.poll();
            // 该元素是否已在HashSet中，在将不操作，否则插入
            if ( !list.contains(val) ) {
                list.add(val);
                PriorityQueue.add( val * 3 );
                PriorityQueue.add( val * 5 );
                PriorityQueue.add( val * 7 );
            }
            // 返回第k个位数值
            if (list.size() == k) {
                return val.intValue();
            }
        }
    }
```

