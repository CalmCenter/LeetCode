给定一个仅包含 `0` 和 `1` 、大小为 `rows x cols` 的二维二进制矩阵，找出只包含 `1` 的最大矩形，并返回其面积。

**示例 1：**

![img](https://assets.leetcode.com/uploads/2020/09/14/maximal.jpg)

```
输入：matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
输出：6
解释：最大矩形如上图所示。
```

**示例 2：**

```
输入：matrix = []
输出：0
```

**示例 3：**

```
输入：matrix = [["0"]]
输出：0
```

**示例 4：**

```
输入：matrix = [["1"]]
输出：1
```

**示例 5：**

```
输入：matrix = [["0","0"]]
输出：0
```

 

**提示：**

- `rows == matrix.length`
- `cols == matrix.length`
- `0 <= row, cols <= 200`
- `matrix[i][j]` 为 `'0'` 或 `'1'`

# 题解 一  暴力破解

求出每个点作为矩形右下角时的最大宽度，当矩形高度提示时，与上一个点计算相对小的宽度，作为矩形的宽来计算面积。

先计算**每个点作为矩形右下角**时的最大宽度，如下

```
[
 ["1","0","1","0","0"],
 ["1","0","1","1","1"],
 ["1","1","1","1","1"],
 ["1","0","0","1","0"]
]

1 0 1 0 0 
1 0 1 2 3
1 2 3 4 5
1 0 0 1 0
```

每个宽度为前一个点 `+1` 计算得来，如果是 `0` 那就是没有宽度。然后开始向上扩展行，已第三行最后一个点为例

高度为 1 ：

```
1 0 1 0 0 
1 0 1 2 3
❶ ❷ ❸ ❹ ❺ ←
1 0 0 1 0
        ↑
```

高度为 2 ：上一行宽度为 `3`  ，比当前行小，所以最大宽度变为 `3` 。

```
1 0 1 0 0 
1 0 ❶ ❷ ❸
1 2 ❸ ❹ ❺ ←
1 0 0 1 0
        ↑
```

代码：

```java
    public int maximalRectangle(char[][] matrix) {

        if (matrix.length == 0) return 0;
        int maxarea = 0;
        int[][] dp = new int[matrix.length][matrix[0].length];

        for(int i = 0; i < matrix.length; i++){
            for(int j = 0; j < matrix[0].length; j++){
                if (matrix[i][j] == '1'){

                    // 计算当前点作为右下角的宽度
                    dp[i][j] = j == 0? 1 : dp[i][j-1] + 1;

                    int width = dp[i][j];

                    // 计算当前点作为右下角，向上扩展高度时的面积，矩形的宽为当前列最小的宽
                    for(int k = i; k >= 0; k--){
                        width = Math.min(width, dp[k][j]);
                        // i - k + 1 表示高度，width 是当前高度所支持的最大宽度
                        maxarea = Math.max(maxarea, width * (i - k + 1));
                    }
                }
            }
        } return maxarea;
    }
```

复杂度分析，其中 `N`为行数， `M` 为列数。

时间复杂度 : O(N^2 M)。由于需要遍历同一列中的值，计算每个点对应最大面积需要O(N)。对全部 N∗M 个点都要计算，因此总共O(N) * O(NM) = O(N^2M)

空间复杂度 : O(NM)。我们分配了一个等大的数组，用于存储每个点的最大宽度。

# 解法二 栈

我们也可以把 `1` 作为 84 题中的柱子，对每一行进行一遍 84 题中求最大柱状图中最大矩形的面积

```
[
 ["1","0","1","0","0"],
 ["1","0","1","1","1"],
 ["1","1","1","1","1"],
 ["1","0","0","1","0"]
]
```

第一行

```
 ■   ■    
 1 2 3 4 5 
```

第二行

```
 ■   ■
 ■   ■ ■ ■   
 1 2 3 4 5 
```

第三行

```
 ■   ■
 ■   ■ ■ ■   
 ■ ■ ■ ■ ■   
 1 2 3 4 5 
```

代码如下

```java
    public int maximalRectangle(char[][] matrix) {
        if (matrix.length == 0) {
            return 0;
        }
        int[] heights = new int[matrix[0].length];
        int maxArea = 0;
        for (int row = 0; row < matrix.length; row++) {
            //遍历每一列，更新高度
            for (int col = 0; col < matrix[0].length; col++) {
                if (matrix[row][col] == '1') {
                    heights[col] += 1;
                } else {
                    heights[col] = 0;
                }
            }
            //调用上一题的解法，更新函数
            maxArea = Math.max(maxArea, largestRectangleArea(heights));
        }
        return maxArea;
    }
     
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

复杂度分析

时间复杂度 : O(NM)。对每一行运行 力扣 84 需要 M (每行长度) 时间，运行了 N 次，共计 O(NM)。

空间复杂度 : O(M)。我们声明了长度等于列数的数组，用于存储每一行的宽度。

