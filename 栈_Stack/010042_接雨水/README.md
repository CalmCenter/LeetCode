## 题目

给定 *n* 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

**示例 1：**

![图片来源于 LeetCode](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/rainwatertrap.png)

```
输入：height = [0,1,0,2,1,0,1,3,2,1,2,1]
输出：6
解释：上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 
```

**示例 2：**

```
输入：height = [4,2,0,3,2,5]
输出：9
```

**提示：**

- `n == height.length`
- `0 <= n <= 3 * 104`
- `0 <= height[i] <= 105`

## 方法一：暴力循环

循环计算当前柱子的左右最高柱子，就可以计算出存水的最高高度，然后减去当前柱子的高度，就是当前位置可以存多少单位的雨水 。

```java
    public int trap(int[] height) {
        int ans = 0;
        int size = height.length;
        // 当前柱子
        for (int i = 1; i < size - 1; i++) {

            int max_left = 0, max_right = 0;
            // 查看当前柱子左边(包括当前柱子)最高的柱子
            for (int j = i; j >= 0; j--) {
                max_left = Math.max(max_left, height[j]);
            }
            // 查看当前柱子右边(包括当前柱子)最高的柱子
            for (int j = i; j < size; j++) { //Search the right part for max bar size
                max_right = Math.max(max_right, height[j]);
            }
            // 木桶装水，最低柱子决定水的高度 - 当前柱子的高度 = 水的高度
            ans += Math.min(max_left, max_right) - height[i];
        }
        return ans;
    }
```

**复杂性分析**

- 时间复杂度： *O*(*n^*2)。数组中的每个元素都需要向左向右扫描。
- 空间复杂度 *O*(1) 的额外空间。

## 方法二：暴力优化，动态编程

在暴力方法中，仅仅为了找到最大值每次都要向左和向右扫描一次。但是我们可以提前存储这个值。因此，可以通过动态编程解决。

```java
    public int trap(int[] height) {
        if (height == null || height.length == 0)
            return 0;
        int ans = 0;
        int size = height.length;
        int[] left_max = new int[size];
        int[] right_max = new int[size];
        // 第一个柱子
        left_max[0] = height[0];
        // 提前计算每个柱子的左端右端最大值
        // 从最左端开始记录每个柱子最大值
        for (int i = 1; i < size; i++) {
            // 左一个柱子的最大值与当前柱子的高度做比较，大的就是当前柱子的左最大值。
            left_max[i] = Math.max(height[i], left_max[i - 1]);
        }
      
        right_max[size - 1] = height[size - 1];
        // 从最右端开始记录每个柱子最大值，由于最后一个肯定是本身所以跳过
        for (int i = size - 2; i >= 0; i--) {
            // 右一个柱子的最大值与当前柱子的高度做比较，大的就是当前柱子的右最大值。
            right_max[i] = Math.max(height[i], right_max[i + 1]);
        }
        for (int i = 1; i < size - 1; i++) {
            // 木桶装水，最低柱子决定水的高度 - 当前柱子的高度 = 水的高度
            ans += Math.min(left_max[i], right_max[i]) - height[i];
        }
        return ans;
    }
```

**复杂性分析**

- 时间复杂度：*O*(*n*)。

  - 存储最大高度数组，需要两次遍历，每次 O(n) 。

  - 最终使用存储的数据更新 ans ， O(n)。

- 空间复杂度：*O*(*n*) 额外空间。

  - 和方法 1 相比使用了额外的 O(n) 空间用来放置 left_max 和 right_max 数组。


## 方法三：栈的应用

用栈来跟踪可能储水的最长的条形块。

遍历数组时维护一个栈。如果当前的条形块**小于或等于**栈顶的条形块，我们将条形块的索引入栈，意思是**当前的条形块被栈中的前一个条形块界定**。如果我们**发现一个条形块长于栈顶**，我们可以确定栈顶的条形块被当前条形块和栈的前一个条形块界定，因此我们可以弹出栈顶元素并且累加答案到 ans 。

```
[ 0 , 3 , 2 , 1 , 1 , 0 , 2 , 3 , 2 , 1 , 2 , 1 ]
  ↑
stack : 0 
```

第一次循环，由于 `pop` 出栈顶后，栈内为空 `if (stack.isEmpty())`  ，所以继续添加栈

`[c] 表示 height[current]  [s] 表示 height[stack.peek()]`

```
[ 0 , 3 , 2 , 1 , 1 , 0 , 2 , 3 , 2 , 1 , 2 , 1 ]
      ↑   ↑
     [s] [c]
current : 2
stack   : 1 

// 根据 height[current] > height[stack.peek()] 最终循环至

[ 0 , 3 , 2 , 1 , 1 , 0 , 2 , 3 , 2 , 1 , 2 , 1 ]
                  ↑   ↑   ↑
                 [s][top][c]
current : 6
stack   : 5 4 3 2 1 
```

`[top]` 表示当前柱子，`[s]` 左边柱子，`[c]` 表示右边柱子。

`distance = current - stack.peek() - 1` 表示 `[s]`左边柱子与`[c]`右边柱子之间有多少个柱子。

`bounded_height = Math.min(height[current], height[stack.peek()])` 找出左右柱子中最低的柱子，然后 `- height[top]`  表示当前柱子(`top`)与两边最低的柱子高度之差（能装水的高度）。

`distance * bounded_height` 表示 **[s]柱子** 到 **[c]柱子** 能装多少个单位的水。

```
distance: 1
bounded_height : 1

        ■
        ■ ■       ■
        ■ ■ ■ ■ □ ■
pos : 0 1 2 3 4 5 6 
hei : 0 3 2 1 1 0 2
              ↑ ↑ ↑
            [s][t][c]
```

然后继续调用内部 `while` 循环，内部 `while` 循环表示将 `[s]` 向左移

根据 `Math.min(height[current], height[stack.peek()]) - height[top]` 当  `[s] = [top]`  表示当前水面无高度，`[s]` 继续向左移。直到：

```
[ 0 , 3 , 2 , 1 , 1 , 0 , 2 , 3 , 2 , 1 , 2 , 1 ]
          ↑   ↑           ↑
         [s][top]        [c]
current : 6
stack   : 2 1 
distance: 3
bounded_height : 1

        ■
        ■ ■ □ □ □ ■
        ■ ■ ■ ■ □ ■
pos : 0 1 2 3 4 5 6 
hei : 0 3 2 1 1 0 2
          ↑ ↑     ↑
        [s][t]   [c]
```

之后类似的逻辑类似，代码如下：

```java
    public int trap(int[] height) {
        int ans = 0, current = 0;
        Deque<Integer> stack = new ArrayDeque<>();
        while (current < height.length) {
            // 查看 height[stack.peek()](左柱子) 是否 < height[current](右柱子)
            while (!stack.isEmpty() && height[current] > height[stack.peek()]) {
                int top = stack.pop();
                if (stack.isEmpty())
                    break;
                // 计算 current(右柱子)  与 stack.peek(左柱子) 之间的距离
                int distance = current - stack.peek() - 1;
                // 获取左右柱子最低的柱子  -  当前柱子(top)的高度 表示 当前柱子与两边最低的柱子高度之差（能装水的高度）
                // 这里 和 while 中的 两次 stack.peek() 取出来的值是不一样的，因为中间 Pop 了一次
                int bounded_height = Math.min(height[current], height[stack.peek()]) - height[top];
                // 高度 * 宽度(距离) = 水占用多少单位
                ans += distance * bounded_height;
            }
            stack.push(current++);
        }
        return ans;
    }
```

**复杂性分析**

- 时间复杂度：O(n)。
  - 单次遍历 O(n) ，每个条形块最多访问两次（由于栈的弹入和弹出），并且弹入和弹出栈都是 O(1) 的。
- 空间复杂度：O(n)。 栈最多在阶梯型或平坦型条形块结构中占用 O(n) 的空间。

## 方法 四：双指针

当我们拿到 `height[left] < height[right]` 这时 `height[left + 1] < height[left]` ，可以说明 `height[left + 1]` 这个柱子肯定存在积水，并且积水的高度为 `left_max - height[left]` 

```
[ 0 , 3 , 2 , 1 , 1 , 0 , 2 , 3 , 2 , 1 , 2 , 1 ]
      ↑                                       ↑
      L                                       R
left_max : 0 
right_max：1

[ 0 , 3 , 2 , 1 , 1 , 0 , 2 , 3 , 2 , 1 , 2 , 1 ]
  ↑   ↑                               ↑   ↑
 Lmax L                               R  Rmax

ans : 1
```

`right_max < height[left]`  并且 `height[right] < right_max ` 证明 `right` 这个柱子会存在积水，高度为 `right_max - height[right]`。

```
[ 0 , 3 , 2 , 1 , 1 , 0 , 2 , 3 , 2 , 1 , 2 , 1 ]
  ↑   ↑                           ↑       ↑
 Lmax L                           R      Rmax

ans : 1

[ 0 , 3 , 2 , 1 , 1 , 0 , 2 , 3 , 2 , 1 , 2 , 1 ]
  ↑   ↑                       ↑   ↑
 Lmax L                       R  Rmax

ans : 1

[ 0 , 3 , 2 , 1 , 1 , 0 , 2 , 3 , 2 , 1 , 2 , 1 ]
  ↑   ↑               ↑       ↑
 Lmax L               R      Rmax

ans : 1 + 1

[ 0 , 3 , 2 , 1 , 1 , 0 , 2 , 3 , 2 , 1 , 2 , 1 ]
  ↑   ↑           ↑           ↑
 Lmax L           R          Rmax

ans : 1 + 1 + 3
```

以此类推，代码如下

```java
    public int trap(int[] height) {
        int left = 0, right = height.length - 1;
        int ans = 0;
        int left_max = 0, right_max = 0;
        while (left < right) {
            if (height[left] < height[right]) {
                if (height[left] >= left_max) {
                    left_max = height[left];
                } else {
                    ans += (left_max - height[left]);
                }
                ++left;
            } else {
                if (height[right] >= right_max) {
                    right_max = height[right];
                } else {
                    ans += (right_max - height[right]);
                }
                --right;
            }
        }
        return ans;
    }
```

复杂性分析

时间复杂度：O(n)。单次遍历的时间O(n)。
空间复杂度：O(1) 的额外空间。left, right, left_max 和 right_max 只需要常数的空间。

