以 `Unix` 风格给出一个文件的**绝对路径**，你需要简化它。或者换句话说，将其转换为规范路径。

在 Unix 风格的文件系统中，一个点`（.）`表示当前目录本身；此外，两个点`（..）` 表示将目录切换到上一级（指向父目录）；两者都可以是复杂相对路径的组成部分。更多信息请参阅：[Linux / Unix中的绝对路径 vs 相对路径](https://blog.csdn.net/u011327334/article/details/50355600)

请注意，返回的规范路径必须始终以斜杠 `/` 开头，并且两个目录名之间必须只有一个斜杠`` /`。最后一个目录名（如果存在）**不能**以 `/` 结尾。此外，规范路径必须是表示绝对路径的**最短**字符串。

**示例 1：**

```
输入："/home/"
输出："/home"
解释：注意，最后一个目录名后面没有斜杠。
```

**示例 2：**

```
输入："/../"
输出："/"
解释：从根目录向上一级是不可行的，因为根是你可以到达的最高级。
```

**示例 3：**

```
输入："/home//foo/"
输出："/home/foo"
解释：在规范路径中，多个连续斜杠需要用一个斜杠替换。
```

**示例 4：**

```
输入："/a/./b/../../c/"
输出："/c"
```

**示例 5：**

```
输入："/a/../../b/../c//.//"
输出："/c"
```

**示例 6：**

```
输入："/a//b////c/d//././/.."
输出："/a/b/c"
```

## 题解

使用 `split` 将字符分开后，要么得到 **空格**，要么得到 **字符**，要么得到 `..` 或是 `. `
使用栈模拟，遍历字符数组，如果是**正常字符**，就**栈入**，如果是 `..` 则**弹出**，如果是 `.` 或是空则 `continue` 
最后遍历栈，将字符拼接在一起返回即可

```java
    public String simplifyPath(String path) {
        String[] pathArray = path.split("/");
        //分割后的几种情况 空 、 字符 、 . 、 ..
        StringBuilder res = new StringBuilder();
        Deque<String> stack = new ArrayDeque<>();
        for (String s : pathArray) {
            //如果长度为 0 或者 . 表示无意义的符号
            if (s.length() == 0 || s.equals(".")) continue;
            //2种情况，栈为空或者栈不为空
            if (!stack.isEmpty()) {
                // 回退
                if (s.equals("..")) {
                    stack.pop();
                } else {
                    stack.push(s);
                }
            } else {
                // 如果是字符
                if (!s.equals("..")) stack.push(s);
            }
        }
        if (stack.isEmpty()) return res.append('/').toString();
        while (!stack.isEmpty()) {
            res.insert(0, stack.pop());
            res.insert(0, '/');
        }
        return res.toString();
    }
```

