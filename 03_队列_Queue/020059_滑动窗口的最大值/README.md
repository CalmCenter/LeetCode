给定一个数组 `nums` 和滑动窗口的大小 `k`，请找出所有滑动窗口里的最大值。

**示例:**

```
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7] 
解释: 

  滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**提示：**

你可以假设 *k* 总是有效的，在输入数组不为空的情况下，1 ≤ k ≤ 输入数组的大小。

注意：本题与主站 239 题相同：https://leetcode-cn.com/problems/sliding-window-maximum/

# 题解 滑动窗口

未形成窗口：

```
nums: 1 3 -1 -3 5 3 6 7
      ↑
      i
deque: 1 
  res:
  
nums: 1 3 -1 -3 5 3 6 7
      ↑ ↑
        i      
deque: 3
  res:
  
nums: 1 3 -1 -3 5 3 6 7
      ↑ ↑  ↑
     i-k   i
deque: 3 -1 
  res: 3
```

形成窗口：

```
nums: 1 3 -1 -3 5 3 6 7
        ↑  ↑  ↑
       i-k    i
deque: 3 -1 -3
  res: 3 3
  
nums: 1 3 -1 -3 5 3 6 7
           ↑  ↑ ↑
          i-k   i
deque: 5
  res: 3 3 5
  
nums: 1 3 -1 -3 5 3 6 7
              ↑ ↑ ↑
             i-k  i
deque: 5 3
  res: 3 3 5 5
  
nums: 1 3 -1 -3 5 3 6 7
                ↑ ↑ ↑
               i-k  i
deque: 6
  res: 3 3 5 5 6
  
  
nums: 1 3 -1 -3 5 3 6 7
                  ↑ ↑ ↑
                 i-k  i
deque: 7
  res: 3 3 5 5 6 7
```





```java
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums.length == 0) return nums;

        Deque<Integer> deque = new LinkedList<>();
        int[] arr = new int[nums.length - k + 1];
        int index = 0;  //arr数组的下标
        //未形成窗口区间
        for (int i = 0; i < k; i++) {
            // 这两个操作 是为了 保证 队列 头部始终为 当前窗口里的最大值
            //队列不为空时，循环 当前值与队列尾部值比较，如果大于，删除队列尾部值
            while (!deque.isEmpty() && nums[i] > deque.peekLast()) deque.removeLast();
            //执行完上面的循环后，队列中要么为空，要么值都比当前值大，然后就把当前值添加到队列中
            deque.addLast(nums[i]);
        }
        // 记录最大值
        arr[index++] = deque.peekFirst();
        //窗口区间形成
        for (int i = k; i < nums.length; i++) {
            // 移动窗口操作 
            // 删除窗口前一个值，i - k 表示窗口里的第一个值，但是可能在 循环比较中已经删除(deque 中已经不存在) ，则需要判断一下 deque 中是否存在需要删除的窗口头
            if (deque.peekFirst() == nums[i - k]) deque.removeFirst();
            // 保证 队列 头部始终为 当前窗口里的最大值
            while (!deque.isEmpty() && nums[i] > deque.peekLast()) deque.removeLast();
            // 添加窗口后一个值，把当前值添加到队列中
            deque.addLast(nums[i]);
            //把队列的首位值添加到arr数组中
            arr[index++] = deque.peekFirst();
        }
        return arr;
    }
```

##### 复杂度分析：

- **时间复杂度 O(n)：**其中 n 为数组 nums 长度；线性遍历 nums 占用 O(N) ；每个元素最多仅入队和出队一次，因此单调队列 deque 占用 O(2N) 。
- **空间复杂度 O(k) ：**双端队列 deque 中最多同时存储 k 个元素（即窗口大小）。

