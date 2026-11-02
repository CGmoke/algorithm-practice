# [3120. 统计特殊字母的数量 I](https://leetcode.cn/problems/count-the-number-of-special-characters-i/)

给你一个字符串 `word`。如果 `word` 中同时存在某个字母的小写形式和大写形式，则称这个字母为 **特殊字母** 。

返回 `word` 中 **特殊字母** 的数量。

**示例 1:**

**输入：**word = "aaAbcBC"

**输出：**3

**解释：**

`word` 中的特殊字母是 `'a'`、`'b'` 和 `'c'`。

**示例 2:**

**输入：**word = "abc"

**输出：**0

**解释：**

`word` 中不存在大小写形式同时出现的字母。

**示例 3:**

**输入：**word = "abBCab"

**输出：**1

**解释：**

`word` 中唯一的特殊字母是 `'b'`。

方法一：集合去重

```python
class Solution:
    def numberOfSpecialChars(self, word: str) -> int:
        word =set(word)
        return sum(1 for x in word if  x in word and chr(ord(x)+32) in word )
```

方法二：

遍历所有大写 ASCII 字母

```python
class Solution:
    def numberOfSpecialChars(self, word: str) -> int:
        return sum(1 for x in ascii_uppercase  if  x in word and chr(ord(x)+32) in word )
```

