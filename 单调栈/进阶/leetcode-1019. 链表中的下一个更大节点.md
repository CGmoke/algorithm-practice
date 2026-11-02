# [1019. 链表中的下一个更大节点](https://leetcode.cn/problems/next-greater-node-in-linked-list/)

给定一个长度为 `n` 的链表 `head`

对于列表中的每个节点，查找下一个 **更大节点** 的值。也就是说，对于每个节点，找到它旁边的第一个节点的值，这个节点的值 **严格大于** 它的值。

返回一个整数数组 `answer` ，其中 `answer[i]` 是第 `i` 个节点( **从1开始** )的下一个更大的节点的值。如果第 `i` 个节点没有下一个更大的节点，设置 `answer[i] = 0` 。

 

**示例 1：**

![linkedlistnext1](./leetcode-1019.%20链表中的下一个更大节点.assets/linkedlistnext1.jpg)

```
输入：head = [2,1,5]
输出：[5,5,0]
```

**示例 2：**

![linkedlistnext2](./leetcode-1019.%20链表中的下一个更大节点.assets/linkedlistnext2.jpg)

```
输入：head = [2,7,4,3,5]
输出：[7,0,5,5,0]
```

 

**提示：**

- 链表中节点数为 `n`
- `1 <= n <= 104`
- `1 <= Node.val <= 109`





```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def nextLargerNodes(self, head: Optional[ListNode]) -> List[int]:
        st = []
        array=[]
        while head:
            array.append(head.val)
            head=head.next
        n = len(array)
        res = [0]*n
        for i in range(n):
            while st and array[i]>array[st[-1]]:
                res[st.pop()] = array[i]
            st.append(i)
        return res
        
```



## 1. 问题回顾

给定一个单向链表，返回一个数组，其中每个元素是链表中对应节点值的 **下一个更大节点值**（即在它之后第一个值比它大的节点的值），如果没有则为 `0`。

------

## 2. 算法思路

### ① 链表转为数组



```python
array = []
while head:
    array.append(head.val)
    head = head.next
```



将链表的所有节点值按顺序存入数组 `array`。
这样就把问题完全转化为：**在数组 `array` 中，对于每个元素，找其右侧第一个更大的元素**。
优势：可以直接利用数组索引和随机访问，方便使用单调栈的标准模板。

### ② 单调栈寻找下一个更大元素



```python
res = [0] * n
st = []
for i in range(n):
    while st and array[i] > array[st[-1]]:
        res[st.pop()] = array[i]
    st.append(i)
```



- **栈的作用**：存放**尚未找到下一个更大元素的元素的下标**。
- **栈的单调性**：从栈底到栈顶，对应数组中的值是**严格递减**的（栈底最大，栈顶最小）。
- 遍历到当前位置 `i` 时，如果当前值 `array[i]` 比栈顶下标对应的值大，说明栈顶元素找到了它的下一个更大值，于是弹出栈顶，并把结果数组对应位置填为 `array[i]`。一直弹出直到栈空或栈顶值 `≥` 当前值。
- 当前下标 `i` 入栈，表示它自己也在等待未来的更大元素。
- 遍历结束后，栈中剩余的下标都是右侧没有更大元素的，它们的结果保持初始化时的 `0`。

### ③ 返回结果

`res` 就是每个节点对应的答案数组，直接返回即可。

------

## 3. 举例说明

链表：`[2, 1, 5]` → 数组 `[2, 1, 5]`

- `i=0, val=2`：栈空，`st=[0]`
- `i=1, val=1`：`1` 不大于栈顶 `2`，`st=[0,1]`
- `i=2, val=5`：`5 > array[1]=1`，弹出 `1`，`res[1]=5`；
  `5 > array[0]=2`，弹出 `0`，`res[0]=5`。栈空，压入 `2`。
  结果 `res = [5, 5, 0]`，正确。

------

## 4. 复杂度分析

- **时间复杂度**：O(n)。链表遍历一次，数组遍历一次，每个元素入栈、出栈各一次。
- **空间复杂度**：O(n)。数组和栈都最多存储所有元素。

------

## 5. 总结

该解法的本质是将链表问题转化为数组问题，再套用 **“单调递减栈求下一个更大元素”** 的通用模板，清晰且高效。这种“先存为数组再用单调栈”的模式，在处理链表的类似问题时很常用。

