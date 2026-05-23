# [624. 数组列表中的最大距离](https://leetcode.cn/problems/maximum-distance-in-arrays/)

##### [中等]

给定 `m` 个数组，每个数组都已经按照升序排好序了。

现在你需要从两个不同的数组中选择两个整数（每个数组选一个）并且计算它们的距离。两个整数 `a` 和 `b` 之间的距离定义为它们差的绝对值 `|a-b|` 。

返回最大距离。

**示例 1：**

```
输入：[[1,2,3],[4,5],[1,2,3]]
输出：4
解释：
一种得到答案 4 的方法是从第一个数组或者第三个数组中选择 1，同时从第二个数组中选择 5 。
```

**示例 2：**

```
输入：arrays = [[1],[1]]
输出：0
```

 

**提示：**

- `m == arrays.length`
- `2 <= m <= 105`
- `1 <= arrays[i].length <= 500`
- `-104 <= arrays[i][j] <= 104`
- `arrays[i]` 以 **升序** 排序。
- 所有数组中最多有 `105` 个整数。

```python
class Solution:
    def maxDistance(self, arrays: List[List[int]]) -> int:
        res= 0
        n = len(arrays)
        min_num = arrays[0][0]
        max_num = arrays[0][-1]
        for i in range(1,n):

            res = max(res,abs(min_num-arrays[i][-1]),abs(max_num-arrays[i][0]))
            min_num = min(min_num,arrays[i][0])
            max_num =max(max_num,arrays[i][-1])
        return res
class Solution:
    def maxDistance(self, arrays: List[List[int]]) -> int:
        res= 0
        n = len(arrays)
        min_num = arrays[0][0]
        max_num = arrays[0][-1]
        for i in range(1,n):
            #可以优化掉abs取绝对值的内置函数来提高运行效率
            res = max(res,arrays[i][-1]-min_num,max_num-arrays[i][0])
            min_num = min(min_num,arrays[i][0])
            max_num =max(max_num,arrays[i][-1])
        return res
```

## 算法思想：贪心 + 一次遍历维护历史极值

### 1. 核心思路

题目要求两个元素**必须来自不同数组**，但每个数组内部都是有序的。
一个直观的想法是：

- 最大差值一定出现在**某个数组的最小值**和**另一个数组的最大值**之间。
- 为了避免选到同一个数组，我们可以在遍历时，**先用之前所有数组的极值，与当前数组的另一个极值计算差值**，再把当前数组的极值纳入“历史极值”中。这样就永远不会用同一个数组的两个极值相减。

### 2. 具体步骤

- 初始化：`min_num = arrays[0][0]`（第一个数组的最小值），`max_num = arrays[0][-1]`（第一个数组的最大值），`res = 0`。
- 从第二个数组 (`i=1`) 开始遍历：
  1. **计算与历史极值的差值**：
     当前数组的最小值为 `arrays[i][0]`，最大值为 `arrays[i][-1]`。
     用历史最大值与当前数组的最小值求差：`abs(max_num - arrays[i][0])`；
     用历史最小值与当前数组的最大值求差：`abs(min_num - arrays[i][-1])`；
     取这两个差值和当前 `res` 的最大值更新 `res`。
     **注意**：这里保证了参与计算的两个元素分别来自“之前的某个数组”和“当前数组”，一定不是同一个数组。
  2. **更新历史极值**：
     把当前数组的最小值、最大值纳入历史范围，即：
     `min_num = min(min_num, arrays[i][0])`
     `max_num = max(max_num, arrays[i][-1])`
- 循环结束后，`res` 就是全局最大绝对差。

### 3. 正确性证明

假设全局最优解的两个元素分别来自数组 `A` 和 `B`（`A` ≠ `B`），不妨设 `A` 出现在 `B` 之前（遍历顺序）。
当我们遍历到 `B` 时，`min_num` 和 `max_num` 已经包含了 `A` 的极值（甚至可能包含了更优的极值，但 `A` 的极值至少已被包含）。此时计算：

- `max_num - B.min` 会考虑到 `A.max` 与 `B.min` 的组合（如果 `A.max` 是历史最大，则直接就是该组合；如果历史最大来自更早的其他数组，差值只会更大或相等）。
- `B.max - min_num` 也会考虑到 `A.min` 与 `B.max` 的组合。

因此，最优解必定会被考虑并计算出正确的差值。

### 4. 复杂度分析

- 时间复杂度：**O(m)**，其中 `m` 是数组个数。只需一次遍历。
- 空间复杂度：**O(1)**，仅使用了常数个变量。