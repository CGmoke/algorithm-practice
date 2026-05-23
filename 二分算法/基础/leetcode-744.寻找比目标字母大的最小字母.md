# [744. 寻找比目标字母大的最小字母](https://leetcode.cn/problems/find-smallest-letter-greater-than-target/)

给你一个字符数组 `letters`，该数组按 **非递减顺序** 排序，以及一个字符 `target`。`letters` 里**至少有两个不同**的字符。

返回 `letters` 中大于 `target` 的最小的字符。如果不存在这样的字符，则返回 `letters` 的第一个字符。

**示例 1：**

```
输入: letters = ['c', 'f', 'j'], target = 'a'
输出: 'c'
解释：letters 中字典上比 'a' 大的最小字符是 'c'。
```

**示例 2:**

```
输入: letters = ['c','f','j'], target = 'c'
输出: 'f'
解释：letters 中字典顺序上大于 'c' 的最小字符是 'f'。
```

**示例 3:**

```
输入: letters = ['x','x','y','y'], target = 'z'
输出: 'x'
解释：letters 中没有一个字符在字典上大于 'z'，所以我们返回 letters[0]。
```

**提示：**

- `2 <= letters.length <= 104`
- `letters[i]` 是一个小写字母
- `letters` 按**非递减顺序**排序
- `letters` 最少包含两个不同的字母
- `target` **是一个小写字母**

```python
class Solution:
    def nextGreatestLetter(self, letters: List[str], target: str) -> str:
        def lower_bound(letters,target):
            left = 0
            right = len(letters)-1
            while left<=right:
                mid = (left+right)//2
                if ord(letters[mid])<=ord(target):
                    left=mid+1
                else:
                    right = mid-1
            return left
        res =lower_bound(letters,target)
        return letters[res] if res!=len(letters) else letters[0]
```

注：在 Python 中，将字符转换为对应的 ASCII 码（正确拼写为 ASCII）可以使用内置函数 `ord()`。

**示例：**

python

```
char = 'A'
ascii_code = ord(char)
print(ascii_code)  # 输出 65
```



如果需要将 ASCII 码转换回字符，则使用 `chr()`：

python

```
code = 65
char = chr(code)
print(char)  # 输出 'A'
```



注意：`ord()` 返回的是 Unicode 码点，对于 ASCII 字符来说与 ASCII 码相同。

### 算法复杂度

- **时间复杂度**：O(log n)，其中 n 是 `letters` 的长度。二分查找每次将搜索范围减半。
- **空间复杂度**：O(1)，仅使用常数个变量。

### 注意事项与扩展

- 由于数组有序且元素可重复，该算法能正确处理重复字母（例如 `letters = ["a","a","b"]`, `target = "a"` 返回 `"b"`）。

- 如果题目要求返回大于等于 `target` 的最小字符，只需将 `<=` 改为 `<` 即可。

- 也可以用 Python 标准库 `bisect` 模块：

  python

  ```python
  import bisect
  idx = bisect.bisect_right(letters, target)  # 第一个大于 target 的位置
  return letters[idx % len(letters)]
  ```