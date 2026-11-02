# [3043. 最长公共前缀的长度](https://leetcode.cn/problems/find-the-length-of-the-longest-common-prefix/)

难度[中等]	标签:字典树	数组	哈希表	字符串

给你两个 **正整数** 数组 `arr1` 和 `arr2` 。

正整数的 **前缀** 是其 **最左边** 的一位或多位数字组成的整数。例如，`123` 是整数 `12345` 的前缀，而 `234` **不是** 。

设若整数 `c` 是整数 `a` 和 `b` 的 **公共前缀** ，那么 `c` 需要同时是 `a` 和 `b` 的前缀。例如，`5655359` 和 `56554` 有公共前缀 `565` 和 `5655`，而 `1223` 和 `43456` **没有** 公共前缀。

你需要找出属于 `arr1` 的整数 `x` 和属于 `arr2` 的整数 `y` 组成的所有数对 `(x, y)` 之中最长的公共前缀的长度。

返回所有数对之中最长公共前缀的长度。如果它们之间不存在公共前缀，则返回 `0` 。

**示例 1：**

```
输入：arr1 = [1,10,100], arr2 = [1000]
输出：3
解释：存在 3 个数对 (arr1[i], arr2[j]) ：
- (1, 1000) 的最长公共前缀是 1 。
- (10, 1000) 的最长公共前缀是 10 。
- (100, 1000) 的最长公共前缀是 100 。
最长的公共前缀是 100 ，长度为 3 。
```

**示例 2：**

```
输入：arr1 = [1,2,3], arr2 = [4,4,4]
输出：0
解释：任何数对 (arr1[i], arr2[j]) 之中都不存在公共前缀，因此返回 0 。
请注意，同一个数组内元素之间的公共前缀不在考虑范围内。 
```

**提示：**

- `1 <= arr1.length, arr2.length <= 5 * 104`
- 1 <= arr1[i], arr2[i] <= 108

#### 解法一：用字符串处理

把 *arr*1 的所有前缀丢到一个哈希集合中，然后遍历 *arr*2 的所有前缀，统计在哈希集合中的最长长度。

```python
class Solution:
    def longestCommonPrefix(self, arr1: List[int], arr2: List[int]) -> int:
        res =  0
        arr1 =  map(str,arr1)
        arr2 =  map(str,arr2)
        st = set()
        for i in arr1:
            for j in range(1,len(i)+1):
                st.add(i[:j])
        for i in arr2:
            for j in range(1,len(i)+1):
                if i[:j] not in st:
                    break
                res = max(res,int(j))
        return res
```

复杂度分析
时间复杂度：O((n+m)log 2U)，其中 n 是 arr 1的长度，m 是 arr 2的长度，U 是数组元素的最大值。值为 x 的数的十进制长度为 O(logx)。
空间复杂度：O(nlog 2 U)。

#### 解法二：用整数处理

不断地把元素 x 除以 10（下取整）直到 0，例如 123→12→1→0，这个过程中的数即为 x 的前缀。

代码实现时，不需要计算长度，而是取数值的最大值，因为数值越大长度越长。

```python
class Solution:
    def longestCommonPrefix(self, arr1: List[int], arr2: List[int]) -> int:
        st = set()
        res = 0
        for i in arr1:
            while i:
                st.add(i)
                i//=10
        
        for i in arr2:
            while i not in st and i:
                i//=10
            res = max(res,i)
        return len(str(res)) if res else 0
        
```

#### 复杂度分析

时间复杂度：O((n+m)logU)，其中 n 是 arr 1的长度，m 是 arr2的长度，U 是数组元素的最大值。
空间复杂度：O(nlogU)。