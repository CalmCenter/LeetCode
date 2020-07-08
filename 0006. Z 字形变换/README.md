## 题目

将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 `"LEETCODEISHIRING"` 行数为 3 时，排列如下：

```
↓ L   C   I   R
↓ E T O E S I I G
↓ E   D   H   N
```

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"LCIRETOESIIGEDHN"` 。

请你实现这个将字符串进行指定行数变换的函数：

```
string convert(string s, int numRows);
```

**示例1:**

```
输入: s = "LEETCODEISHIRING", numRows = 3
输出: "LCIRETOESIIGEDHN"
```

**示例2:**

```
输入: s = "LEETCODEISHIRING", numRows = 4
输出: "LDREOEIIECIHNTSG"
解释:

L     D     R
E   O E   I I
E C   I H   N
T     S     G
```

## 解题

### 方法  1 ：按行排序

思路

创建 `min(numRows,len(s))` 个 `StringBuilder` 保存每行的数据。

第一个字符，在遍历字符的时候，从上往下取 `StringBuilder`

```java
L  E  E  T  C  O  D  E
↑

→ StringBuilder1 L
  StringBuilder2
  StringBuilder3
------------------------------------
L  E  E  T  C  O  D  E
   ↑

  StringBuilder1 L
→ StringBuilder2 E
  StringBuilder3
------------------------------------
L  E  E  T  C  O  D  E
      ↑

  StringBuilder1 L
  StringBuilder2 E
→ StringBuilder3 E
```

只有当 ` StringBuilder` 指针向上移动到最上面的行或向下移动到最下面的行时，当前方向才会发生改变。

```java
L  E  E  T  C  O  D  E
         ↑

  StringBuilder1 L
→ StringBuilder2 ET
  StringBuilder3 E
------------------------------------
L  E  E  T  C  O  D  E
            ↑
  
→ StringBuilder1 LC
  StringBuilder2 ET
  StringBuilder3 E
------------------------------------
L  E  E  T  C  O  D  E
               ↑
  
  StringBuilder1 LC
→ StringBuilder2 ETO
  StringBuilder3 E
```

具体代码如下。

```java
public String convert(String s, int numRows) {

    if (numRows == 1) return s;

    List<StringBuilder> rows = new ArrayList<>();
    for (int i = 0; i < Math.min(numRows, s.length()); i++)
        rows.add(new StringBuilder());

    int curRow = 0;
    boolean goingDown = false;

    for (char c : s.toCharArray()) {
        rows.get(curRow).append(c);
        //只有当 curRow 向上移动到最上面的行或向下移动到最下面的行时，当前方向才会发生改变。
        if (curRow == 0 || curRow == numRows - 1) goingDown = !goingDown;
        curRow += goingDown ? 1 : -1;
    }

    StringBuilder ret = new StringBuilder();
    for (StringBuilder row : rows) ret.append(row);
    return ret.toString();
}
```

**复杂度分析**

- 时间复杂度：*O*(*n*)，其中 *n*==len(*s*)
- 空间复杂度：*O*(*n*)

### 方法  2 ：按行访问

从数据中可以看出，第一行和最后一行是有规律的，第一行每一个下标相差 `2 * numRows - 2` 位， `-2`  中一个 `-1` 是减去缺少的一个字符，一个 `-1` 是表示下标。

```
L   C
E T
E
```

代码中的 `i` 表示 行， `j` 表示长列（不是每一列）。`i0` 表示当前 `i` 下标为 `0`。

```java
i0 →  L   C   I   R
i1    E T O E S I I G
i2    E   D   H   N
      ↑   ↑   ↑   ↑
      j0  j4  j8  
```

可以发现第一行`L 和 C ` 、第二行`E 和 O ` 、第三行`E 和 D ` 相差都是 `2 * numRows - 2` 位

那每一长列中间的数应该怎么表示呢？
第二行 `E` 表示方法为，当前行数 + 当前列数 ` i1 + j0 ` 
第二行 `O` 表示方法为，当前行数 + E 所在列数 + 加上长列差 ` i1 + j0 + cycleLen` 
第二行 `S` 表示方法为，当前行数 + O 所在列数 + 加上长列差 ` i1 + j4 + cycleLen` 
所以，
第二行 `T` 表示方法为，`E` 的下标 - `2`   ` i1 + j0 + cycleLen - 2` ，以此类推。

但是如果是 `4` 行就有问题了。

```
      L     D     R
      E   O E   I I
i2 →  E C   I H   N
      T     S     G
      ↑     ↑
      j0    j6
```

`i2` 行 `E I N` 的计算没有问题，但计算 `C ` 的时候 `i2 + j0 + cycleLen - 2` 是错误的，需要减去 `4` 。

如果你列出 `5` 行 `6` 行的数据，发现最后减的都是行数下标的两倍。

简化为 `j0 + cycleLen - i2` 。理解过来就是 `i0` 行的 `D` 减去两位就是 `C` 位置。

```java
    public String convert(String s, int numRows) { 
        if (numRows == 1) return s;
        StringBuilder ret = new StringBuilder();
        int n = s.length();
      	//cycleLen 表示每行在 长列 的位置相差多少位。
        int cycleLen = 2 * numRows - 2;

        for (int i = 0; i < numRows; i++) {
            for (int j = 0; j + i < n; j += cycleLen) {
                ret.append(s.charAt(j + i));
                if (i != 0 && i != numRows - 1 && j + cycleLen - i < n)
                    ret.append(s.charAt(j + cycleLen - i));
            }
        }
        return ret.toString();
    }
```

复杂度分析

时间复杂度：O(n)，其中 n==len(s)。每个索引被访问一次。
空间复杂度：O(n)。

