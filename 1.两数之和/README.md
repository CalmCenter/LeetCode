## 题目

给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

**示例:**

```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```



## 解题

### 方法一：暴力法

暴力法很简单，遍历每个元素 x，并查找是否存在一个值与 target - x 相等的目标元素。

```java
public int[] twoSum(int[] nums, int target) {
    for (int i = 0; i < nums.length; i++) {
        for (int j = i + 1; j < nums.length; j++) {
            if (nums[j] == target - nums[i]) {
                return new int[]{i, j};
            }
        }
    }
    throw new IllegalArgumentException("No two sum solution");
}
```

**复杂度分析：**

时间复杂度：*O*(n^2)
对于每个元素，我们试图通过遍历数组的其余部分来寻找它所对应的目标元素，这将耗费 O(n) 的时间。因此时间复杂度为 *O*(n^2)

空间复杂度：*O*(1)。

---

### 方法二：两遍哈希表

通过以空间换取速度的方式，我们可以将查找时间从 *O*(n) 降低到 *O*(1)。

一个简单的实现使用了两次迭代。在第一次迭代中，我们将每个元素的值和它的索引添加到表中。然后，在第二次迭代中，我们将检查每个元素所对应的目标元素（target - nums[i]）是否存在于表中。注意，该目标元素不能是 nums[i]  本身！

```java
public int[] twoSum2(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        map.put(nums[i], i);
    }
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement) && map.get(complement) != i) {
            return new int[]{i, map.get(complement)};
        }
    }
    throw new IllegalArgumentException("No two sum solution");
}
```

复杂度分析：

时间复杂度：*O*(n)，
我们把包含有 n 个元素的列表遍历两次。由于哈希表将查找时间缩短到 *O*(1) ，所以时间复杂度为 *O*(n)。

空间复杂度：*O*(n)，
所需的额外空间取决于哈希表中存储的元素数量，该表中存储了 n 个元素。

---

### 方法三：一遍哈希表

在进行迭代并将元素插入到表中的同时，我们还会回过头来检查表中是否已经存在当前元素所对应的目标元素。如果它存在，那我们已经找到了对应解，并立即将其返回。

```java
public int[] twoSum3(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[]{map.get(complement), i};
        }
        map.put(nums[i], i);
    }
    throw new IllegalArgumentException("No two sum solution");
}
```

时间复杂度：*O*(n)，
我们只遍历了包含有 n 个元素的列表一次。在表中进行的每次查找只花费 *O*(1) 的时间。

空间复杂度：*O*(n)，
所需的额外空间取决于哈希表中存储的元素数量，该表最多需要存储 n 个元素。

力扣执行结果：

执行用时：3 ms, 在所有 Java 提交中击败了 75.86% 的用户

内存消耗：39.7 MB, 在所有 Java 提交中击败了 5.06% 的用户

---

### 方法四：一遍数组

这是运算最快的，但是这个有个限制，就是 `target` 和 `nums` 中的数不能大于等于 `volume` ，不然会出现重复数。
比如 2049 & 2047 = 1，1 & 2047 = 1。

原理和方法三差不多，只是不用哈希表，用了数组，数组下标对应的数为 1 就表示已存在，也表示找到了对应的解，利用数组加快了速度，但有了一个限制。

```java
public int[] twoSum4(int[] nums, int target) {
    int volume = 2048; //100000000000
    int bitMode = volume - 1;//011111111111
    int[] result = new int[volume];
    for (int i=0;i<nums.length;i++){
        int c = (target - nums[i]) & bitMode;//防止数组越界
        if (result[c]!=0){
            return new int[]{result[c]-1,i};
        }
        result[nums[i] & bitMode]=i+1;
    }
    throw new IllegalArgumentException("No two sum solution");
}
```

时间复杂度：*O*(n)，
我们只遍历了包含有 n 个元素的列表一次。在表中进行的每次查找只花费 *O*(1) 的时间。

空间复杂度：*O*(n)，
所需的额外空间取决于哈希表中存储的元素数量，该表最多需要存储 n 个元素。

力扣执行结果：

执行用时：0 ms, 在所有 Java 提交中击败了 100.00% 的用户

内存消耗：39.6 MB, 在所有 Java 提交中击败了 5.06% 的用户

---

## 加量不加价

方法二三中用到了 `HashMap` 中的一个 `containsKey` 函数

```java
final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

这里边有一个 `do while` 循环，但它不是肯定走循环的，只有在遍历冲突走链表的时候才会走循环。同时如果冲突频繁，会改用 `getTreeNode` 方法去获取值，`getTreeNode` 是从一棵红黑树中获取值, 时间复杂度顶多 `O(logN)`