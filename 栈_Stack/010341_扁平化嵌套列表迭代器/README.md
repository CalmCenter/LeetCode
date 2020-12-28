给你一个嵌套的整型列表。请你设计一个迭代器，使其能够遍历这个整型列表中的所有整数。

列表中的每一项或者为一个整数，或者是另一个列表。其中列表的元素也可能是整数或是其他列表。

**示例 1:**

```
输入: [[1,1],2,[1,1]]
输出: [1,1,2,1,1]
解释: 通过重复调用 next 直到 hasNext 返回 false，next 返回的元素的顺序应该是: [1,1,2,1,1]。
```

**示例 2:**

```
输入: [1,[4,[6]]]
输出: [1,4,6]
解释: 通过重复调用 next 直到 hasNext 返回 false，next 返回的元素的顺序应该是: [1,4,6]。
```

**NestedInteger**

```java
public interface NestedInteger {
    // 如果其中存的是一个整数，则返回 true，否则返回 false
    public boolean isInteger();

    // 如果其中存的是一个整数，则返回这个整数，否则返回 null
    public Integer getInteger();

    // 如果其中存的是一个列表，则返回这个列表，否则返回 null
    public List<NestedInteger> getList();
}
```

## 题解 递归

把一个 `NestedInteger` 扁平化，**这不就等价于遍历一棵 N 叉树的所有「叶子节点」吗**？把所有叶子节点都拿出来，不就可以作为迭代器进行遍历了吗？

`N` 叉树的遍历

```java
void traverse(TreeNode root) {
    for (TreeNode child : root.children)
        traverse(child);
```

把 `List<NestedInteger> ` 一次性提取到 `List` 当中，再使用 `ListIterator` 的 `hasNext` 和 `next` 即可。

```java
class NestedIterator implements Iterator<Integer> {

    private Iterator<Integer> it;
    
    public NestedIterator(List<NestedInteger> nestedList) {
        // 存放将 nestedList 打平的结果
        List<Integer> result = new LinkedList<>();
        for (NestedInteger node : nestedList) {
            // 以每个节点为根遍历
            traverse(node, result);
        }
        // 得到 result 列表的迭代器
        this.it = result.iterator();
    }

    public Integer next() {
        return it.next();
    }

    public boolean hasNext() {
        return it.hasNext();
    }    
    
    // 遍历以 root 为根的多叉树，将叶子节点的值加入 result 列表
    private void traverse(NestedInteger root, List<Integer> result) {
        if (root.isInteger()) {
            // 到达叶子节点
            result.add(root.getInteger());
            return;
        }
        // 遍历框架
        for (NestedInteger child : root.getList()) {
            traverse(child, result);
        }
    }
}
```

## 迭代器

上面的解法中一次性算出了所有叶子节点的值，全部装到 `result` 列表，也就是内存中，`next` 和 `hasNext` 方法只是在对 `result` 列表做迭代。如果输入的规模非常大，构造函数中的计算就会很慢，而且很占用内存。

一般的迭代器求值应该是「惰性的」，也就是说，如果你要一个结果，我就算一个（或是一小部分）结果出来，而不是一次把所有结果都算出来。

**调用 `hasNext` 时，如果 `nestedList` 的第一个元素是列表类型，则不断展开这个元素，直到第一个元素是整数类型**。

由于调用 `next` 方法之前一定会调用 `hasNext` 方法，这就可以保证每次调用 `next` 方法的时候第一个元素是整数型，直接返回并删除第一个元素即可。

```java
public class NestedIterator implements Iterator<Integer> {
    private LinkedList<NestedInteger> list;

    public NestedIterator(List<NestedInteger> nestedList) {
        // 不直接用 nestedList 的引用，是因为不能确定它的底层实现
        // 必须保证是 LinkedList，否则下面的 addFirst 会很低效
        list = new LinkedList<>(nestedList);
    }

    public Integer next() {
        // hasNext 方法保证了第一个元素一定是整数类型
        return list.remove(0).getInteger();
    }

    public boolean hasNext() {
        // 循环拆分列表元素，直到列表第一个元素是整数类型
        while (!list.isEmpty() && !list.get(0).isInteger()) {
            // 当列表开头第一个元素是列表类型时，进入循环
            List<NestedInteger> first = list.remove(0).getList();
            // 将第一个列表打平并按顺序添加到开头
            for (int i = first.size() - 1; i >= 0; i--) {
                list.addFirst(first.get(i));
            }
        }
        return !list.isEmpty();
    }
}
```

