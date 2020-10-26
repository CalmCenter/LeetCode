## 题目

编写一个程序，找到两个单链表相交的起始节点。

示例 1:

![](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Reference of the node with value = 8
输入解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。

```

示例 2:

![](https://assets.leetcode.com/uploads/2018/12/13/160_example_2.png)

```
输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出：Reference of the node with value = 2
输入解释：相交节点的值为 2 （注意，如果两个链表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。

```

示例 3:

![](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)

```
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
输入解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
解释：这两个链表不相交，因此返回 null。

```



## 方法：双指针法

- 指针 `pA` 指向 `A` 链表，指针 `pB` 指向 `B` 链表，依次往后遍历
- 如果 `pA` 到了末尾，则 `pA = headB` 继续遍历
- 如果 `pB` 到了末尾，则 `pB = headA` 继续遍历
- 比较长的链表指针指向较短链表 `head`  时，长度差就消除了
- 如此，只需要将最短链表遍历两次即可找到位置

初始化

```
  pA
   ↓
   1 -> 2 -> 3 -> 4
                   \
  				         5 -> 6 -> null
                   /
                 9			
                 ↑
                 pB
```

开始遍历

```
       pA
        ↓
A: 1 -> 2 -> 3 -> 4
                   \
  				         5 -> 6 -> null
                   /↑
B:               9	pB	
                 
            pA
             ↓
   1 -> 2 -> 3 -> 4
                   \
  				         5 -> 6 -> null
                   /     ↑
                 9	    pB	          
                 
                 pA
                  ↓
   1 -> 2 -> 3 -> 4
                   \
  				         5 -> 6 -> null
                   /           ↑
                 9	          pB	    
```

`pB` 到了链表末尾，指向 `链表A` 

```
  pB             
   ↓              
   1 -> 2 -> 3 -> 4 pA
                   \↓
  				         5 -> 6 -> null
                   /           
                 9	         	 

       pB             
        ↓              
   1 -> 2 -> 3 -> 4     pA
                   \     ↓
  				         5 -> 6 -> null
                   /           
                 9	   
                 
            pB             
             ↓              
   1 -> 2 -> 3 -> 4           pA
                   \           ↓
  				         5 -> 6 -> null
                   /           
                 9	   

                 pB             
                  ↓              
   1 -> 2 -> 3 -> 4           
                   \           
  				         5 -> 6 -> null
                   /           
                 9	
                 ↑
                pA

                             
                               
   1 -> 2 -> 3 -> 4 pB         
                   \↓           
  				         5 -> 6 -> null
                   /↑           
                 9	pA
                 
```

如果有相交点 则会 `pA = pB` 如果没有相交点，则会形成下面这样。`pB` 指向 `A` 链表末尾，`pA` 指向 `B`  链表末尾。

```
                       pB
                        ↓
A: 1 -> 2 -> 3 -> 4 -> null              
B:           9 -> 5 -> null
                        ↑
                        pA
```

若相交，链表 `A`： `a+c`, 链表 `B`  : `b+c` 
`a+c+b+c ` = `b+c+a+c ` 则会在公共处c起点相遇。
若不相交，`a+b`  = `b+a` 。因此相遇处是 `NULL` 

代码：

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    if (headA == null || headB == null) return null;
    ListNode pA = headA, pB = headB;
    while (pA != pB) {
        pA = pA == null ? headB : pA.next;
        pB = pB == null ? headA : pB.next;
    }
    return pA;
}

```

**复杂度分析**

- 时间复杂度 : *O*(*m*+*n*)。
- 空间复杂度 : *O*(1)