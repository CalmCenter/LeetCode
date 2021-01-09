给定一个循环数组（最后一个元素的下一个元素是数组的第一个元素），输出每个元素的下一个更大元素。数字 x 的下一个更大的元素是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 -1。

**示例 1:**

```
输入: [1,2,1]
输出: [2,-1,2]
解释: 第一个 1 的下一个更大的数是 2；
数字 2 找不到下一个更大的数； 
第二个 1 的下一个最大的数需要循环搜索，结果也是 2。
```

## 题解

倒序

```java
    public int[] nextGreaterElements(int[] nums) {
        int[] res = new int[nums.length];
        Stack<Integer> stack = new Stack<>();
        // 因为是一个 循环数组 ，需要循环两次来确认下一个大值，使用倒序，在栈顶值 > 当前值时，说明栈顶值就是下一个大值
        for (int i = 2 * nums.length - 1; i >= 0; --i) {
            // 栈 不为空，并且 栈顶值 <= 当前值，则出栈
            while (!stack.empty() && nums[stack.peek()] <= nums[i % nums.length]) {
                stack.pop();
            }
            // 当前栈顶就是 下一个大值，否则为 -1
            res[i % nums.length] = stack.empty() ? -1 : nums[stack.peek()];
            stack.push(i % nums.length);
        }
        return res;
    }
```

正序

```java
public int[] nextGreaterElements2(int[] nums) {
    int[] res = new int[nums.length];
    Arrays.fill(res, -1);
    Deque<Integer> stack = new LinkedList<>();   // 存储的是索引index
    // 由于是循环数组,对于末位元素,要往后循环考虑到其前一位数也就是倒数第二个数
    for(int i = 0; i < 2 * nums.length; i++) {
        // 对于当前元素,弹出栈中小于当前元素的元素,这些被弹出的元素的"下一个更大元素"就是当前元素
        // 一个元素只有入栈后再被弹出,才能得到该元素的"下一个更大元素",否则无"下一个更大元素",res[i]默认为-1
        // 例如,对于最大的元素,其一直留在栈中无法被弹出,因此其"下一个更大元素"默认为-1
        while(!stack.isEmpty() && nums[stack.peek()] < nums[i % nums.length])
            res[stack.pop()] = nums[i % nums.length];
        stack.push(i % nums.length);
    }
    return res;
}
```

**复杂度分析**

- 时间复杂度：O(N)。
- 空间复杂度：O(N)。