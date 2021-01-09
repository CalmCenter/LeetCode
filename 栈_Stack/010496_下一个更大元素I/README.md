给定两个 **没有重复元素** 的数组 `nums1` 和 `nums2` ，其中`nums1` 是 `nums2` 的子集。找到 `nums1` 中每个元素在 `nums2` 中的下一个比其大的值。

`nums1` 中数字 **x** 的下一个更大元素是指 **x** 在 `nums2` 中对应位置的右边的第一个比 **x** 大的元素。如果不存在，对应位置输出 -1 。

**示例 1:**

```
输入: nums1 = [4,1,2], nums2 = [1,3,4,2].
输出: [-1,3,-1]
解释:
    对于num1中的数字4，你无法在第二个数组中找到下一个更大的数字，因此输出 -1。
    对于num1中的数字1，第二个数组中数字1右边的下一个较大数字是 3。
    对于num1中的数字2，第二个数组中没有下一个更大的数字，因此输出 -1。
```

**示例 2:**

```
输入: nums1 = [2,4], nums2 = [1,2,3,4].
输出: [3,-1]
解释:
    对于 num1 中的数字 2 ，第二个数组中的下一个较大数字是 3 。
    对于 num1 中的数字 4 ，第二个数组中没有下一个更大的数字，因此输出 -1 。
```

**提示：**

1. `nums1`和`nums2`中所有元素是唯一的。
2. `nums1`和`nums2` 的数组大小都不超过1000。

## 题解

- `nums1` 是 `nums2` 的子集
- 找到 `nums1` 中每个元素在 `nums2` 中的下一个比其大的值

题意就是要遍历 `nums1` 中所有的元素，并且找到该元素在 `nums2` 的位置，然后找到在该位置右边比该元素大的值。

当然还有种方式是，我们可以忽略数组 `nums1`，先对将 `nums2` 中的每一个元素，求出其下一个更大的元素。随后对于将这些答案放入哈希映射`（HashMap）` 中，再遍历数组 `nums1`，并直接找出答案。

```java
    public int[] nextGreaterElement(int[] findNums, int[] nums) {
        Deque<Integer> stack = new LinkedList<>();
        HashMap<Integer, Integer> map = new HashMap<>();
        int[] res = new int[findNums.length];
        // 遍历 nums2 填充 map
        for (int num : nums) {
            // 栈不为空 并且 当前值 > 栈顶的值
            while (!stack.isEmpty() && num > stack.peek())
                // 保存 栈顶值 对应的 下一个更大的元素
                map.put(stack.pop(), num);
            stack.push(num);
        }

        while (!stack.isEmpty())
            map.put(stack.pop(), -1);
        // 遍历 num1 在 map 中寻找对应的 下一个更大的元素
        for (int i = 0; i < findNums.length; i++) {
            res[i] = map.get(findNums[i]);
        }
        return res;
    }
```

复杂度分析

时间复杂度：`O(M+N)`，其中 `M` 和 `N` 分别是数组 `nums1` 和 `nums2` 的长度。

空间复杂度：`O(N)`。我们在遍历 `nums2` 时，需要使用栈，以及哈希映射用来临时存储答案。

