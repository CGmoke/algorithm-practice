# 在排序数组中查找元素的第一个和最后一个位置

给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 `target`，返回 `[-1, -1]`。

你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。

 

**示例 1：**

```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

**示例 2：**

```
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
```

**示例 3：**

```
输入：nums = [], target = 0
输出：[-1,-1]
```

 

**提示：**

- `0 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`
- `nums` 是一个非递减数组
- `-109 <= target <= 109`

相关标签

[数组](https://leetcode.cn/tag/array/)[二分查找](https://leetcode.cn/tag/binary-search/)

```python
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        res = []
        def lower_bound(nums,target):
            n = len(nums)
            left = 0
            right = n-1
            while left<=right:
                mid = (left+right)//2
                if nums[mid]>=target:
                    right = mid -1
                else:
                    left = mid+1
            return left
        start =lower_bound(nums,target)
        if start==len(nums) or nums[start]!=target:
            return[-1,-1]
        end = lower_bound(nums,target+1)-1
        res= [start,end]
        return res

```

## 1. 整体思路

题目要求在排序数组中找到 `target` 的左右边界，如果不存在则返回 `[-1, -1]`。
代码的核心是：

1. 写一个通用的 `lower_bound` 函数：返回 **第一个 ≥ x 的元素下标**。
2. 用 `lower_bound(nums, target)` 找到 **左边界** `start`。
3. 如果 `start` 越界 或 `nums[start] != target`，说明 `target` 不存在，直接返回 `[-1, -1]`。
4. 否则，用 `lower_bound(nums, target + 1) - 1` 得到 **右边界** `end`。
   - `lower_bound(nums, target+1)` 返回第一个 ≥ `target+1` 的位置，即第一个 **大于** `target` 的位置。
   - 将其减 1，就是最后一个 ≤ `target` 的位置，也就是右边界。

这种“一函数两用”的写法非常经典，简洁且不容易出错。

------

## 2. `lower_bound` 函数分析

python

```python
def lower_bound(nums, target):
    n = len(nums)
    left = 0
    right = n - 1          # 闭区间 [left, right]
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] >= target:
            right = mid - 1
        else:
            left = mid + 1
    return left
```



- **区间定义**：`[left, right]` 是闭区间，循环继续条件为 `left <= right`。
- **更新逻辑**：
  - `nums[mid] >= target` → 说明 `mid` 可能是答案，但左边可能还有符合条件的，所以收缩右边界 `right = mid - 1`。
  - `nums[mid] < target` → `mid` 及左边全不满足，收缩左边界 `left = mid + 1`。
- **最终返回**：循环退出时 `left > right`，`left` 恰好指向第一个 `≥ target` 的位置（若所有元素都小于 `target`，则 `left == n`，正好作为越界判断）。

该实现是标准的“二分查找下界”模板，正确且高效。

------

## 3. 主函数逻辑分析

python

```python
start = lower_bound(nums, target)
if start == len(nums) or nums[start] != target:
    return [-1, -1]
end = lower_bound(nums, target + 1) - 1
res = [start, end]
return res
```



**查找左边界**：

- `start = lower_bound(nums, target)` 得到第一个 `≥ target` 的索引。
- 若 `start == len(nums)` 说明所有数都小于 `target`；
  若 `nums[start] != target` 说明不存在等于 `target` 的元素。
  这两种情况均返回 `[-1, -1]`。

**查找右边界**：

- `lower_bound(nums, target + 1)` 找到第一个 `≥ target+1` 的位置，即第一个 **严格大于** `target` 的位置。
- 该位置减 1 即为最后一个等于 `target` 的位置（右边界）。
- 因为左边界已经确认存在 `target`，所以右边界一定有效，不会出现 `end < start` 的情况（即使 `target` 只出现一次，`start == end` 也是正确的）。

------

## 4. 边界与特殊情况

| 情况            | 示例                                      | 结果      | 说明                                                         |
| :-------------- | :---------------------------------------- | :-------- | :----------------------------------------------------------- |
| 空数组          | `nums = []`                               | `[-1,-1]` | `start = 0`（因为 `left=0,right=-1` 不进入循环），`start == len(nums)` 成立，返回 `-1,-1`。 |
| 目标不在数组中  | `nums=[1,3], target=2`                    | `[-1,-1]` | `start = 1`（`nums[1]=3≥2`），但 `nums[1]!=2`，返回 `-1,-1`。 |
| 目标在数组头/尾 | `nums=[2,2,3], target=2`                  | `[0,1]`   | 正常工作。                                                   |
| 全数组都是目标  | `nums=[7,7,7], target=7`                  | `[0,2]`   | `start=0`, `end=3-1=2`，正确。                               |
| 目标值极大      | `target` 接近 `2^31-1`，`target+1` 溢出？ | 无问题    | Python 的整数不会溢出，自动升级为长整型。                    |

空数组的处理值得注意：

- `n = 0`，`right = -1`，`while left <= right` 不执行，直接 `return left`（0）。
- `start == 0 == len(nums)`，触发 `return [-1,-1]`，正确。

------

## 5. 时间复杂度

- `lower_bound` 是对数级二分查找，时间复杂度 **O(log n)**。
- 主函数调用了两次 `lower_bound`，整体仍为 **O(log n)**。
- 空间复杂度：只用常数个额外变量，**O(1)**。