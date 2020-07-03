## 题目

给定两个大小为 `m` 和 `n` 的正序（从小到大）数组 `nums1` 和 `nums2`。

请你找出这两个正序数组的中位数，并且要求算法的时间复杂度为 `O(log(m + n))`。

你可以假设 `nums1` 和 `nums2` 不会同时为空。

**示例1:**

```
nums1 = [1, 3]
nums2 = [2]

则中位数是 2.0
```

**示例2:**

```
nums1 = [1, 2]
nums2 = [3, 4]

则中位数是 (2 + 3)/2 = 2.5
```

## 解题

### 方法 1：二分法

思路：

两个数组总长度需要分奇偶数
根据中位数的定义，
当 `m+n` 是奇数时，中位数是两个有序数组中的第 `(m+n)/2+1` 个元素，
当 `m+n` 是偶数时，中位数是两个有序数组中的第 `(m+n)/2` 个元素和第 `(m+n)/2+1` 个元素的平均值。

本意就是寻找第 `(m+n)/2` 或第 `(m+n)/2+1`  小的值。可以用 `remaining` 表示还需要找到几个最小值。

我们可以找最小值并标记已找到，标记一个值 `remaining - 1` 。


（可以用 `findMin1` 标记第一个数组中找到了几个最小值）

现在就有两种返回情况：

**情况 1：**

删除直到 `remaining = 1` 的时候， 只要找到两个数组剩余值中最小的就是我们要找的数。

**情况 2：**

当其中一个数组在删除的时候，被删除完了（`deleted1` = 数组1的长度）。那么我们要找的中位数肯定在第二个数组中。
位置就是 `deleted2+remaining-1` （`deleted2` 表示数组2中已经删除的个数，再加上还需要找多少个最小值 `remaining`，由于是下标所以要 `-1`）

相当于找到最小值然后删除，往 `remaining` 里填数，填一个数 `remaining` - 1，知道 `remaining` = 1的时候，

举个栗子🌰

```java
A: 1 3 4 9
B: 1 2 3 4 5 6 7 8 9
```

两个有序数组的长度分别是 `4` 和 `9`，长度之和是 `13`，中位数是两个有序数组中的第 `7` 个元素，那我们就需要找到最小的 `7` 个值。（`remaining`  = 7）

将需要找到的剩余个数平分给两个数组（将工作量平分给两个数组）。

```java
A: 1 3 4 9
       ↑
B: 1 2 3 4 5 6 7 8 9
       ↑
```

`remaining / 2` 每个数组从第 `3` 个开始比较（因为是下标所以是 `remaining / 2 - 1`）。

为什么要平分并从分到的个数作为下标做比较呢？
因为平分后将每个数组的下标定位到 `remaining / 2` ，然后比较，小的一方直接就可以找到最小的 `remaining / 2` 个值。
二分法也体现在这里。

通过`A[2]` 和 B[2] 的比较 `3 < 4` ，又由于数组是有序的，所以我们就找到 `3` 个最小值啦！就将这三个标记，假装不在这个数组里。
然后计算新一轮还需要找多少个最小的值，`remaining - remaining / 2（7 - 3 = 4）`，我们还需要找最小的四个值。

然后重复二分法。每个数组各两个。

```java
A: 1 3 4 9
     ↑
B: [1 2 3] 4 5 6 7 8 9
             ↑
```

又可以找到最小的 `2` 个值。数组 `A` 中的 `[1 3]`。 `remaining = 4 - 2 = 2`。那就是每个数组各一个。

```java
A: [1 3] 4 9
         ↑
B: [1 2 3] 4 5 6 7 8 9
           ↑
```

相同情况标记标记哪个都可以，这里默认标记 `A` 数组。

```java
A: [1 3 4] 9
           ↑
B: [1 2 3] 4 5 6 7 8 9
           ↑
```

然后剩余要找到的最小值就剩一个了，出现了情况 1。所以，拿出两个数组中剩余的第一个值，小的就是我们的最后一个最小值。也就是这里的中位数。

```java
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int length1 = nums1.length, length2 = nums2.length;
        int totalLength = length1 + length2;
        if (totalLength % 2 == 1) {
            return getKthElement(nums1, nums2, totalLength / 2 + 1);
        } else {
            return (getKthElement(nums1, nums2, totalLength / 2) + getKthElement(nums1, nums2, totalLength / 2 + 1)) / 2.0;
        }
    }

    /**
     * @param nums1     第一个数组
     * @param nums2     第二个数组
     * @param remainingMin 剩余几个最小值没有找到
     * @return center
     */
    public int getKthElement(int[] nums1, int[] nums2, int remainingMin) {
        int length1 = nums1.length, length2 = nums2.length;
        //findMin1 数组1中已经找到几个最小值
        //被 findMin1 标记的下标之前的数据都已经没有意义。
        int findMin1 = 0, findMin2 = 0;

        while (true) {
            // 边界情况
            // 如果数组 1 已经找到的最小值 = 数组长度,则数组 1 中都不可能是中位数
            if (findMin1 == length1) {
                //那中位数就是 数组2中已经找到的数量 + 还需要找的数量 - 1(由于是下标)
                return nums2[findMin2 + remainingMin - 1];
            }
            if (findMin2 == length2) {
                return nums1[findMin1 + remainingMin - 1];
            }
            //如果 需要找的最小值只剩一个
            if (remainingMin == 1) {
                //去除已经找到的最小值，比较两个数组中第一个值，谁小
                return Math.min(nums1[findMin1], nums2[findMin2]);
            }

            // 正常情况
            // 将需要找的最小值剩余个数平分到两个数组，并从分到的个数作为下标做比较
            // 为什么要平分并从分到的个数作为下标做比较呢？
            // 因为平分后将每个数组的下标定位到 remaining / 2 ，然后比较，小的一方直接就可以找到 remaining / 2 个最小值。
            // 二分法也体现在这里。
            int half = remainingMin / 2;
            //拿到需要比较的值
            //每次确认 half 个最小值。所以下标每次都要从 (已经找到的最小值+half-1) 开始。
            //这里做了最大值为 length 处理
            int newIndex1 = Math.min(findMin1 + half, length1) - 1;
            int newIndex2 = Math.min(findMin2 + half, length2) - 1;
            int pivot1 = nums1[newIndex1], pivot2 = nums2[newIndex2];
            //比较两个值，代表了 自己 以及 之前所有的值 的荣誉之战，小的一方将全部被标记为最小值。
            if (pivot1 <= pivot2) {
                //找到的总数 - 已经找到的数量 = 新找到的数量
                // 由于是个数(newIndex1 是下标)所以要 +1
                remainingMin -= (newIndex1 - findMin1 + 1);
                //更新已经找到最小值的数量
                findMin1 = newIndex1 + 1;
            } else {
                remainingMin -= (newIndex2 - findMin2 + 1);
                findMin2 = newIndex2 + 1;
            }
        }
    }
```

**复杂度分析**

时间复杂度：*O*(log(m+n))，其中 `m` 和 `n` 分别是数组 `nums1` 和 `nums2` 的长度。初始时有 `k=(m+n)/2` 或 `k=(m+n)/2+1`，每一轮循环可以将查找范围减少一半，因此时间复杂度是 *O*(log(m+n))。

空间复杂度：*O*(1)。

### 方法 2：数组





```java
    public double findMedianSortedArrays2(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays2(nums2, nums1);
        }

        int m = nums1.length;
        int n = nums2.length;
        int left = 0, right = m, ansi = -1;
        // median1：前一部分的最大值
        // median2：后一部分的最小值
        int median1 = 0, median2 = 0;

        while (left <= right) {
            // 前一部分包含 nums1[0 .. i-1] 和 nums2[0 .. j-1]
            // 后一部分包含 nums1[i .. m-1] 和 nums2[j .. n-1]
            int i = (left + right) / 2;
            int j = (m + n + 1) / 2 - i;

            // nums_im1, nums_i, nums_jm1, nums_j 分别表示 nums1[i-1], nums1[i], nums2[j-1], nums2[j]
            int nums_im1 = (i == 0 ? Integer.MIN_VALUE : nums1[i - 1]);
            int nums_i = (i == m ? Integer.MAX_VALUE : nums1[i]);
            int nums_jm1 = (j == 0 ? Integer.MIN_VALUE : nums2[j - 1]);
            int nums_j = (j == n ? Integer.MAX_VALUE : nums2[j]);

            if (nums_im1 <= nums_j) {
                ansi = i;
                median1 = Math.max(nums_im1, nums_jm1);
                median2 = Math.min(nums_i, nums_j);
                left = i + 1;
            } else {
                right = i - 1;
            }
        }

        return (m + n) % 2 == 0 ? (median1 + median2) / 2.0 : median1;
    }
```

