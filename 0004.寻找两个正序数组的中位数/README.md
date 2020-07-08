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

然后重复二分法。 `remaining / 2 = 2` 每个数组各两个。

```java
A: 1 3 4 9
     ↑
B: [1 2 3] 4 5 6 7 8 9
             ↑
```

又可以找到最小的 `2` 个值。数组 `A` 中的 `[1 3]`。 `remaining = 4 - 2 = 2`。 `remaining / 2 = 1`那就是每个数组各一个。

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

思路：

首先取第一个数组一半的位置（向下取整） `int min1 = (left + right) / 2;`  暂定数组 `A` 中有两个小值
所有数组 `B` 需要找到剩下的所有小值（向上取整） `int min2 = (length1 + length2 + 1) / 2 - min1;` 

这里命名为 `min` 是因为，这里在下图中其实是下标，按数量讲的话就标记了这个下标之前有几个小值。

我们需要做的是在数组 `A` 中找到合适的 `min1` 使得 `B[min2 -1] ≤ A[min1]` 且 `A[min1 -1] ≤ B[min2]`

上面需要做的，等价于  `A[min1 -1] ≤ B[min2]` ，我们只需要记录最后一组成立的中位线位置就可以。
为什么等价？比如 `1 3 4 | 9` 和 `1 2 3 4 | 5 6 7 8 9 `  ，当比较完 `4 和 5` 之后，为什么不要比较 `4 和 9` 呢。
其实不是没有比较，只是移动中位线之后才比较。类似于下面两次比较过程。

每次成立让 `min1 + 1` 让 `min2 -1` ，不成立让 `min1 - 1` 让 `min2 + 1`  

```java
A: 1 3 | 4 9
         ↑ 
B: 1 2 3 4 5 | 6 7 8 9 
               ↑ 
```

第一次比较，这里判断 `3 < 6`  成立，所以记录分割线 **左边的最大值 5 ** 和 **右边的最小值 4** 并且移动两个数组的指针。

```java
A: 1 3 4 | 9
           ↑ 
B: 1 2 3 4 | 5 6 7 8 9 
             ↑ 
```

第二次比较，这里判断 `4 < 5`  成立，所以记录分割线 **左边的最大值 4 ** 和 **右边的最小值 5** 并且移动两个数组的指针。

```java
A: 1 3 4 9 | ∞
             ↑ 
B: 1 2 3 | 4 5 6 7 8 9 
           ↑ 
```

第三次比较，这里 `9 > 4`  ，不记录分割线，数组 `A` 也循环完了，所以中位线就是第二次比较时记录的 `4` 和 `5` 。

```java
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        if (nums1.length > nums2.length) {
            return findMedianSortedArrays2(nums2, nums1);
        }

        int length1 = nums1.length;
        int length2 = nums2.length;
        //第一个数组的左右指针
        int left = 0, right = length1, ansi = -1;
        // median1：前一部分的最大值
        // median2：后一部分的最小值
        int median1 = 0, median2 = 0;

        while (left <= right) {
            // 前一部分包含 nums1[0 .. min1-1] 和 nums2[0 .. min2-1]
            // 后一部分包含 nums1[min1 .. length1-1] 和 nums2[min2 .. length2-1]
            int min1 = (left + right) / 2;
            int min2 = (length1 + length2 + 1) / 2 - min1;//找到剩下的所有小值

            // nums1_left 表示第一个数组分割线左边  nums1_right 表示第一个数组 分割线右边
            int nums1_left = (min1 == 0 ? Integer.MIN_VALUE : nums1[min1 - 1]);
            int nums1_right = (min1 == length1 ? Integer.MAX_VALUE : nums1[min1]);
            int nums2_left = (min2 == 0 ? Integer.MIN_VALUE : nums2[min2 - 1]);
            int nums2_right = (min2 == length2 ? Integer.MAX_VALUE : nums2[min2]);

            if (nums1_left <= nums2_right) {
                ansi = min1;
                median1 = Math.max(nums1_left, nums2_left);
                median2 = Math.min(nums1_right, nums2_right);
                left = min1 + 1;
            } else {
                right = min1 - 1;
            }
        }
        //如果两个数组长度是偶数，则让中位线左右两边的数加起来除2，就是最终的中位数
        //由于找到剩下的所有小值时是向上取整，左边比右边多一个数。所以如果是奇数，中位线左边的数就是中位数
        return (length1 + length2) % 2 == 0 ? (median1 + median2) / 2.0 : median1;
    }
```

**复杂度分析** 

时间复杂度：*O*(log min(m,n)))，其中 m 和 n 分别是数组 nums1 和  nums2 的长度。查找的区间是 [0,m]，而该区间的长度在每次循环之后都会减少为原来的一半。所以，只需要执行 log m 次循环。由于每次循环中的操作次数是常数，所以时间复杂度为 *O*(log m)。由于我们可能需要交换 nums1 和  m≤n ，因此时间复杂度是 *O*(log min(m,n)))。

空间复杂度：O(1)。

