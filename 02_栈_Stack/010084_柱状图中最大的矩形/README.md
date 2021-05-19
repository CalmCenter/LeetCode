给定 n 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

 ![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/histogram.png)

以上是柱状图的示例，其中每个柱子的宽度为 1，给定的高度为 [2,1,5,6,2,3]。

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/histogram_area.png)

图中阴影部分为所能勾勒出的最大矩形面积，其面积为 `10` 个单位。

**示例:**

```
输入: [2,1,5,6,2,3]
输出: 10
```

##  方法一 暴力解法

依次遍历柱形的高度，对于每一个高度分别向两边扩散，求出以当前高度为矩形的最大宽度多少。

查看左边柱形，看最多能向左延伸多长，找到大于等于当前柱形高度的最左边元素的下标；

查看右边柱形，看最多能向右延伸多长；找到大于等于当前柱形高度的最右边元素的下标。

```java
    public int largestRectangleArea(int[] heights) {
        int len = heights.length;
        if (len == 0) {
            return 0;
        }

        int res = 0;
        for (int i = 0; i < len; i++) {

            // 找最靠左的并且高度大于等于当前柱形(i) 的下标
            int left = i;
            int curHeight = heights[i];
            while (left > 0 && heights[left - 1] >= curHeight) {
                left--;
            }

            // 找最靠右的并且高度大于等于当前柱形(i) 的下标
            int right = i;
            while (right < len - 1 && heights[right + 1] >= curHeight) {
                right++;
            }

            int width = right - left + 1;
            res = Math.max(res, width * curHeight);
        }
        return res;
    }

```

**复杂度分析**：

- 时间复杂度：*O*(*N^*2)，这里 *N* 是输入数组的长度。
- 空间复杂度：*O*(1)。

## 方法二：单调栈

方法一中，缺点就是每个柱子都有跟重复使用，没有记录，导致时间复杂度为 *O*(*N^*2) ，优化的思路就是「以空间换时间」。

1. 只有一个高度为 `2` 的柱子时，高度还不能确定，我们需要继续遍历

```
      ■
    ■ ■
    ■ ■
    ■ ■   ■
■   ■ ■ ■ ■
■ ■ ■ ■ ■ ■
0 1 2 3 4 5
2 1 5 6 2 3
↑
```

2. 然后看到高度为 `1` 的柱形，这个时候以这个柱形为高度的矩形的最大面积还是不知道的。**但是它之前的以 2 为高度的最大面积的矩形是可以确定的**，这是因为这个 1 比 2 小 ，这个 1 卡在了这里 2 不能再向右边扩展了。所以下标为 `0` 的柱形，最大矩形面积为 `2` 。

```
      ■
    ■ ■
    ■ ■
    ■ ■   ■
□   ■ ■ ■ ■
□ ■ ■ ■ ■ ■
0 1 2 3 4 5
2 1 5 6 2 3
  ↑
```

3. 下一个高度是 `5` ，因为 `5` 比 `1` 大，所以高度为 `1` 的柱形面积确定不了，还不确定右边能不能扩展 ，当前柱形的最大面积也是不知道的，还不确定右边能不能扩展，因为我们还要看右边高度的情况。

```
      ■
    ■ ■
    ■ ■
    ■ ■   ■
□   ■ ■ ■ ■
□ ■ ■ ■ ■ ■
0 1 2 3 4 5
2 1 5 6 2 3
    ↑
```

4. 下一个高度是 `6`  ，同样的，柱形 `1`、`5`、`6` 为高度的最大矩形面积还是不能确定，还不确定右边能不能扩展 
5. 下一个高度是 `2`，这是高度为 `6` 的右边无法扩展了，所以**高度为 `6` 的柱形对应的最大矩形的面积的宽度可以确定下来**，它就是夹在高度为 `5` 的柱形和高度为 `2` 的柱形之间的距离，它的高度是 `6`，宽度是 `1`。

```
      □
    ■ □
    ■ □
    ■ □   ■
□   ■ □ ■ ■
□ ■ ■ □ ■ ■
0 1 2 3 4 5
2 1 5 6 2 3
        ↑
```

这时，**高度为 `5` 的柱形最大面积也可以确定了**，它是夹在高度为 `1` 和高度为 `2` 的两个柱形之间

```
      □
    □ □
    □ □
    □ □   ■
□   □ □ ■ ■
□ ■ □ □ ■ ■
0 1 2 3 4 5
2 1 5 6 2 3
        ↑
```

每次是遇到了当前柱形的高度比它上一个柱形的高度**严格小**的时候，一定可以确定它之前的某些柱形的最大宽度，两个柱形的下标的差就是这个面积最大的矩形对应的最大宽度。

6. 最后一个高度为 `3` ，`3 >= 2` 同样确定不了最大面积 

7. 一次遍历完成以后。接下来考虑栈里的元素全部出栈。

   这个时候计算宽度应该假设最右边还有一个下标为 `len` （这里等于 6） 的高度为 0 的柱形。下标为 5 ，即高度为 3 的柱形，左边的下标是 4 ，右边的下标是 6 ，因此宽度是 6 - 4 - 1 = 1

```
      □
    □ □
    □ □
    □ □   □
□   □ □ ■ □
□ ■ □ □ ■ □
0 1 2 3 4 5 6
2 1 5 6 2 3 0
            ↑
```

8. 下标为 4 ，高度为 2 的柱形，左边的下标是 1 ，右边的下标是 6 ，因此宽度是 6 - 1 - 1 = 4

```
      □
    □ □
    □ □
    □ □   □
□   □ □ □ □
□ ■ □ □ □ □
0 1 2 3 4 5 6
2 1 5 6 2 3 0
            ↑
```

9. 最后看下标为 1，高度为 1 的矩形，它的左边和右边其实都没有元素了，它就是整个柱形数组里高度最低的柱形，计算它的宽度，就是整个柱形数组的长度。

这个算法经过一次遍历，在每一次计算最大宽度的时候，没有去遍历，而是使用了栈里存放的下标信息，以 *O*(1) 的时间复杂度计算最大宽度。

代码：

```java
    public int largestRectangleArea(int[] heights) {
        int len = heights.length;
        if (len == 0) {
            return 0;
        }
        if (len == 1) {
            return heights[0];
        }

        int res = 0;
        Deque<Integer> stack = new ArrayDeque<>(len);
        for (int i = 0; i < len; i++) {
            // 这个 while 很关键，因为有可能不止一个柱形的最大宽度可以被计算出来
            while (!stack.isEmpty() && heights[i] < heights[stack.peekLast()]) {
                int curHeight = heights[stack.pollLast()];
                // 如果高度相同可以一起计算
                while (!stack.isEmpty() && heights[stack.peekLast()] == curHeight) {
                    stack.pollLast();
                }

                int curWidth;
                if (stack.isEmpty()) {
                    curWidth = i;
                } else {
                    curWidth = i - stack.peekLast() - 1;
                }
              
                res = Math.max(res, curHeight * curWidth);
            }
            stack.addLast(i);
        }

        while (!stack.isEmpty()) {
            int curHeight = heights[stack.pollLast()];
            while (!stack.isEmpty() && heights[stack.peekLast()] == curHeight) {
                stack.pollLast();
            }
            int curWidth;
            if (stack.isEmpty()) {
                curWidth = len;
            } else {
                curWidth = len - stack.peekLast() - 1;
            }
            res = Math.max(res, curHeight * curWidth);
        }
        return res;
    }
```

还有一种方法是添加哨兵，之前讲的在两边加入两个 `0` 元素，可以去除上面方法中最后的全部出栈

```java
    public int largestRectangleArea(int[] heights) {
        int len = heights.length;
        if (len == 0) {
            return 0;
        }

        if (len == 1) {
            return heights[0];
        }

        int res = 0;

        // 前后加入 0 元素 可以去除最后的全部出栈
        int[] newHeights = new int[len + 2];
        newHeights[0] = 0;
        System.arraycopy(heights, 0, newHeights, 1, len);
        newHeights[len + 1] = 0;
        len += 2;
        heights = newHeights;

        Deque<Integer> stack = new ArrayDeque<>(len);
        // 先放入哨兵，在循环里就不用做非空判断
        stack.addLast(0);

        for (int i = 1; i < len; i++) {
            while (heights[i] < heights[stack.peekLast()]) {
                int curHeight = heights[stack.pollLast()];
                int curWidth = i - stack.peekLast() - 1;
                res = Math.max(res, curHeight * curWidth);
            }
            stack.addLast(i);
        }
        return res;
    }
```

**复杂度分析**

- 时间复杂度：O(N)。
- 空间复杂度：O(N)。