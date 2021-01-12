给定一个化学式`formula`（作为字符串），返回每种原子的数量。

原子总是以一个大写字母开始，接着跟随 `0` 个或任意个小写字母，表示原子的名字。

如果数量大于 `1`，原子后会跟着数字表示原子的数量。如果数量等于 `1` 则不会跟数字。例如，`H2O` 和 `H2O2` 是可行的，但 `H1O2` 这个表达是不可行的。

两个化学式连在一起是新的化学式。例如 `H2O2He3Mg4` 也是化学式。

一个括号中的化学式和数字（可选择性添加）也是化学式。例如 `(H2O2)` 和 `(H2O2)3` 是化学式。

给定一个化学式，输出所有原子的数量。格式为：第一个（按字典序）原子的名子，跟着它的数量（如果数量大于 `1`），然后是第二个原子的名字（按字典序），跟着它的数量（如果数量大于 `1`），以此类推。

**示例 1:**

```
输入: 
formula = "H2O"
输出: "H2O"
解释: 
原子的数量是 {'H': 2, 'O': 1}。
```

**示例 2:**

```
输入: 
formula = "Mg(OH)2"
输出: "H2MgO2"
解释: 
原子的数量是 {'H': 2, 'Mg': 1, 'O': 2}。
```

**示例 3:**

```
输入: 
formula = "K4(ON(SO3)2)2"
输出: "K4N2O14S4"
解释: 
原子的数量是 {'K': 4, 'N': 2, 'O': 14, 'S': 4}。
```

**注意:**

- 所有原子的第一个字母为大写，剩余字母都是小写。
- `formula`的长度在`[1, 1000]`之间。
- `formula`只包含字母、数字和圆括号，并且题目中给定的是合法的化学式。

# 题解一 递归

- 添加 `parse` 方法来解析化学式，使用 `count` 哈希表保存元素名称和个数
- 设置全局变量 `i` ，标记当前字符下标
- 当遇到 `(` 时，递归解析其中内容，直到括号结束
- 否则，则应该遇到一个大写字符：我们将解析其余的字母以获得名称，并在哈希表中添加该字符（若表中存在则增加计数）。
- 最终，我们将乘以括号系数以得到最终结果。

```java
    int i;
    public String countOfAtoms(String formula) {
        StringBuilder ans = new StringBuilder();
        i = 0;
        Map<String, Integer> count = parse(formula);
        for (String name: count.keySet()) {
            ans.append(name);
            int multiplicity = count.get(name);
            if (multiplicity > 1) ans.append("" + multiplicity);
        }
        return new String(ans);
    }

    public Map<String, Integer> parse(String formula) {
        int N = formula.length();
        Map<String, Integer> count = new TreeMap();
        while (i < N && formula.charAt(i) != ')') {
            if (formula.charAt(i) == '(') {
                i++;
                // 括号 内的内容 使用递归去检索，然后添加到外层 TreeMap
                for (Map.Entry<String, Integer> entry: parse(formula).entrySet()) {
                    count.put(entry.getKey(), count.getOrDefault(entry.getKey(), 0) + entry.getValue());
                }
            } else {
                int iStart = i++;
                // 当前字母后面有几个小写字母，检索当前元素长度
                while (i < N && Character.isLowerCase(formula.charAt(i))) i++;
                // 提取当前元素
                String name = formula.substring(iStart, i);
                iStart = i;
                // 检索当前元素个数的字符
                while (i < N && Character.isDigit(formula.charAt(i))) i++;
                // 提取个数
                int multiplicity = iStart < i ? Integer.parseInt(formula.substring(iStart, i)) : 1;
                // 加入 TreeMap
                count.put(name, count.getOrDefault(name, 0) + multiplicity);
            }
        }
        // 处理括号后的数量
        int iStart = ++i;
        // 检索是否有数量
        while (i < N && Character.isDigit(formula.charAt(i))) i++;
        if (iStart < i) {
            // 将当前存在的元素 * 数量
            int multiplicity = Integer.parseInt(formula.substring(iStart, i));
            for (String key: count.keySet()) {
                count.put(key, count.get(key) * multiplicity);
            }
        }
        return count;
    }
```

**复杂度分析**

- 时间复杂度：O(N2)。N 指的是化学式的长度。
- 空间复杂度：O(N)，我们没有记录到比公式更多的信息。

# 题解二 栈

```java
    public String countOfAtoms2(String formula) {
        int length = formula.length();
        Deque<Map<String, Integer>> stack = new LinkedList<>();
        stack.push(new TreeMap());

        for (int i = 0; i < length;) {
            if (formula.charAt(i) == '(') {
                stack.push(new TreeMap());
                i++;
            } else if (formula.charAt(i) == ')') {
                // 取出括号内的 Map
                Map<String, Integer> current = stack.pop();
                int iStart = ++i, multiplicity = 1;
                // 处理括号后的数量
                while (i < length && Character.isDigit(formula.charAt(i))) i++;
                if (i > iStart) multiplicity = Integer.parseInt(formula.substring(iStart, i));
                for (String element: current.keySet()) {
                    int amount = current.get(element);
                    // 将 current 中的元素 添加到 上层的 map ，
                    // 考虑到可能存在相同元素 stack.peek().getOrDefault(element, 0) 表示之前已经存在的元素数量，默认为 0 即不存在
                    stack.peek().put(element, stack.peek().getOrDefault(element, 0) + amount * multiplicity);
                }
            } else {
                int iStart = i++;
                // 当前字母后面有几个小写字母，检索当前元素长度
                while (i < length && Character.isLowerCase(formula.charAt(i))) i++;
                // 提取当前元素
                String name = formula.substring(iStart, i);
                iStart = i;
                // 检索当前元素个数的字符
                while (i < length && Character.isDigit(formula.charAt(i))) i++;
                // 提取个数
                int multiplicity = i > iStart ? Integer.parseInt(formula.substring(iStart, i)) : 1;
                // 加入 TreeMap
                stack.peek().put(name, stack.peek().getOrDefault(name, 0) + multiplicity);
            }
        }

        // 将栈内的 map 变为字符串
        StringBuilder ans = new StringBuilder();
        for (String name: stack.peek().keySet()) {
            ans.append(name);
            int multiplicity = stack.peek().get(name);
            if (multiplicity > 1) ans.append("" + multiplicity);
        }
        return new String(ans);
    }
```

**复杂度分析**

- 时间复杂度：O(N2)。
- 空间复杂度：O(N)，分析与方法 1 相同。