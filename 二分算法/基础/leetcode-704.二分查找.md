# 704.二分查找

给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target` ，写一个函数搜索 `nums` 中的 `target`，如果 `target` 存在返回下标，否则返回 `-1`。

你必须编写一个具有 `O(log n)` 时间复杂度的算法。


**示例 1:**

```
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
```

**示例 2:**

```
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
```

 

**提示：**

1. 你可以假设 `nums` 中的所有元素是不重复的。
2. `n` 将在 `[1, 10000]`之间。
3. `nums` 的每个元素都将在 `[-9999, 9999]`之间。

```python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        def lower_bound(nums: List[int], target: int)-> int:
            n = len(nums)
            right = n-1
            left =0
            while left<=right:
                mid = (left+right)//2
                if nums[mid]<target:
                    left = mid+1
                else :
                    right = mid-1
            return left
        i = lower_bound(nums,target)
        return i if nums[i]==target else -1
```

#### 复杂度分析

- 时间复杂度：O(log*n*)，其中 *n* 为 *nums* 的长度。
- 空间复杂度：O(1)。