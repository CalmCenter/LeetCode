给定一个用字符串表示的整数的嵌套列表，实现一个解析它的语法分析器。

列表中的每个元素只可能是整数或整数嵌套列表

**提示**：你可以假定这些字符串都是格式良好的：

- 字符串非空
- 字符串不包含空格
- 字符串只包含数字`0-9`、`[`、`-`、`,`、`]`

**示例 1：**

```
给定 s = "324",
你应该返回一个 NestedInteger 对象，其中只包含整数值 324。
```

**示例 2：**

```
给定 s = "[123,[456,[789]]]",
返回一个 NestedInteger 对象包含一个有两个元素的嵌套列表：

1. 一个 integer 包含值 123
2. 一个包含两个元素的嵌套列表：
    i.  一个 integer 包含值 456
    ii. 一个包含一个元素的嵌套列表
         a. 一个 integer 包含值 789
```



```java
public interface NestedInteger {
    // 构造函数初始化一个空的嵌套列表.
//    public NestedInteger();

    // 构造函数初始化一个整数.
//    public NestedInteger(int value);

    // @return true 如果这个 NestedInteger 保存的是整数。
    public boolean isInteger();

    // @return 如果它持整数的话，则返回这个整数，否则返回 null
    public Integer getInteger();

    // 这个 NestedInteger 保存单个整数
    public void setInteger(int value);

    // 这个 NestedInteger 来保存一个嵌套列表，并向其添加一个嵌套整数。
    public void add(NestedInteger ni);
  
    // @return 如果它持有一个嵌套列表的话,则返回这个列表，否则返回 null
    public List<NestedInteger> getList();
}
```

题意是，根据一个字符串，返回一个 `NestedInteger` ，`NestedInteger` 中有两个参数，一个是整数，一个是 `List<NestedInteger>` 。

## 题解

```java
class Solution {
    //递归函数通过字符数组和 cur 下标确定要处理的位置
    char[] chars;
    int cur = 0;
    public NestedInteger deserialize(String s) {
        chars = s.toCharArray();
        //本身不是一个集合而是一个整数的情况
        if(chars[0]!='[') return new NestedInteger(Integer.valueOf(s));
        //调用递归函数返回根集合
        return getNest();
    }
  
    public NestedInteger getNest() {
        NestedInteger nest = new NestedInteger();
        int num = 0;// num 用于缓存用逗号分割的整数类型的值
        int sign = 1;//当前记录的整数的符号，1 代表整数，-1 代表负数
        while (cur != chars.length - 1) {
            cur++;
            if (chars[cur] == ',') continue;
            if (chars[cur] == '[') nest.add(getNest());// 遇到 [ 递归获取子集合
            else if (chars[cur] == ']') return nest;
            else if (chars[cur] == '-') sign = -1;
            else {// 是数字的情况
                num = 10 * num + sign * (chars[cur] - '0');
                // 如果下一个字符是,或者 ] 说明当前数字已经记录完了，需要加入集合中
                if (chars[cur + 1] == ',' || chars[cur + 1] == ']') {
                    nest.add(new NestedInteger(num));
                    num = 0;
                    sign = 1;
                }
            }
        }
        return null;
    }
}

```

