# [3121. 统计特殊字母的数量 II](https://leetcode.cn/problems/count-the-number-of-special-characters-ii/)

给你一个字符串 `word`。如果 `word` 中同时出现某个字母 `c` 的小写形式和大写形式，并且 **每个** 小写形式的 `c` 都出现在第一个大写形式的 `c` 之前，则称字母 `c` 是一个 **特殊字母** 。

返回 `word` 中 **特殊字母** 的数量。

**示例 1:**

**输入：**word = "aaAbcBC"

**输出：**3

**解释：**

特殊字母是 `'a'`、`'b'` 和 `'c'`。

**示例 2:**

**输入：**word = "abc"

**输出：**0

**解释：**

`word` 中不存在特殊字母。

**示例 3:**

**输入：**word = "AbBCab"

**输出：**0

**解释：**

`word` 中不存在特殊字母。

**提示：**

- `1 <= word.length <= 2 * 105`
- `word` 仅由小写和大写英文字母组成。



```python
class Solution:
    def numberOfSpecialChars(self, word: str) -> int:
        last_lower=[-1]*26
        first_upper =[float('inf')]*26
        res=0
        for i,x in enumerate(word):
            if 'a'<=x<='z':
                idx = ord(x)-ord('a')
                last_lower[idx]=max(last_lower[idx],i)
            else:
                idx = ord(x)-ord('A')
                first_upper[idx]= min(first_upper[idx],i)
        for i in range(26):
            if first_upper[i]!=float('inf') and last_lower[i]!=-1 and last_lower[i]<first_upper[i]:
                res+=1
        return res 
```





# 算法思想

## 状态定义

用两个长度为 26 的数组：

- `last_lower[26]`：记录**每个小写字母最后一次出现的索引**（初始为 `-1`，表示还未出现）。
- `first_upper[26]`：记录**每个大写字母第一次出现的索引**（初始为 `float('inf')`，表示还未出现）。

## 一次遍历扫描

对 `word` 逐字符遍历：

- 若当前字符是小写字母 `ch`：计算字母序号 `idx = ord(ch) - 'a'`，更新 `last_lower[idx] = max(last_lower[idx], i)`，即记录该小写字母最靠右的出现位置。
- 若当前字符是大写字母 `ch`：计算字母序号 `idx = ord(ch) - 'A'`，更新 `first_upper[idx] = min(first_upper[idx], i)`，即记录该大写字母最靠左的出现位置。

这样，在一次遍历结束后，我们就得到了每个字母的**小写最后位置**与**大写最早位置**。

##  判断特殊字母

对于每个字母 `i`（0~25）：

- 必须同时存在小写和大写，即 `last_lower[i] != -1` 且 `first_upper[i] != float('inf')`。
- 必须满足 **所有小写出现在所有大写之前**，即 `last_lower[i] < first_upper[i]`（小写的最后位置也小于大写的第一个位置）。
- 满足条件则 `res++`。

## 返回结果

最终 `res` 就是特殊字母的个数。

------

## 正确性分析

- **充分性**：若 `last_lower[i] < first_upper[i]`，则意味着该字母的每个小写出现位置都在最后一个大写出现之前（因为小写最后位置 < 大写最早位置），自然所有小写都在所有大写之前，不存在大写后出现小写的情况。
- **必要性**：若存在一个大写后出现小写，则至少有一个小写索引 > 某大写索引，这样小写的最后位置一定会大于大写的最早位置，即 `last_lower[i] > first_upper[i]`，条件不满足。
- 因此该条件精确刻画了题目要求。

------

## 复杂度分析

- **时间复杂度**：O(n)，仅需遍历字符串一次，再遍历固定长度的数组 26 次，整体线性。
- **空间复杂度**：O(1)，使用两个固定大小的数组（26 个元素），常数级额外空间。

------

## 算法思想总结

该算法巧妙地将“特殊字母”的条件转化为**维护每个字母大小写出现的最值位置**，通过一次扫描收集这些最值信息，再在常数时间内完成判断，避免了多次扫描或嵌套循环，是典型的**离线统计 + 条件映射**的线性优化技巧。