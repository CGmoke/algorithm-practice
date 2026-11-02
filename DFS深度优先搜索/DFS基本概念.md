深度优先搜索（Depth-First Search，DFS）是最基础也是最强大的算法之一。从二叉树的遍历，到复杂的图论问题、回溯剪枝，都离不开DFS的身影。以下我会从最基础的概念讲起，一步步带你掌握DFS的核心思想、实现方式、常见应用以及进阶技巧。

## 一、什么是深度优先搜索？

想象你站在一个迷宫的入口，面前有多条岔路。你选择一条路一直往前走，**不撞南墙不回头**，走到尽头再原路返回，尝试下一个分岔口。这就是深度优先搜索的核心——**沿着一条路径尽可能深入地探索，直到无法继续，再回溯到上一个节点尝试其他路径**。

在数据结构中，DFS一般用于**树**和**图**的遍历，按“先纵深，后横向”的顺序访问节点。

**生活类比**：走迷宫、整理嵌套文件夹、网页爬虫抓取深层链接，本质上都是深度优先的。

------

## 二、树的DFS遍历（最基础）

树是图的一种特例（连通、无环），非常适合用来入门DFS。

### 2.1 前序遍历（Preorder）

**顺序**：根 -> 左子树 -> 右子树

text

```
    1
   / \
  2   3
 / \
4   5

前序：1 2 4 5 3
```



递归实现（Python）：

python

```python
def preorder(root):
    if not root:
        return
    print(root.val)       # 访问根
    preorder(root.left)   # 深入左子树
    preorder(root.right)  # 深入右子树
```



### 2.2 中序遍历（Inorder）

**顺序**：左子树 -> 根 -> 右子树

python

```python
def inorder(root):
    if not root:
        return
    inorder(root.left)
    print(root.val)
    inorder(root.right)
```



### 2.3 后序遍历（Postorder）

**顺序**：左子树 -> 右子树 -> 根

python

```python
def postorder(root):
    if not root:
        return
    postorder(root.left)
    postorder(root.right)
    print(root.val)
```



这三种遍历是DFS思想在树上的直接体现。你会发现，代码结构完全一致，只是访问节点的时机不同。这种“递归”就是DFS最自然的实现形式。

**关键点：递归的本质是系统帮我们维护了一个栈，每次进入下一层相当于压栈，函数返回相当于弹栈，这完美模拟了DFS的回溯过程。**

------

## 三、图的DFS遍历

图比树复杂，可能存在环以及不连通的情况，因此遍历时需要**标记已访问节点**，防止死循环。

### 3.1 递归实现

假设图用邻接表存储：

python

```python
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D', 'E'],
    'C': ['A', 'F'],
    'D': ['B'],
    'E': ['B', 'F'],
    'F': ['C', 'E']
}
visited = set()

def dfs(node):
    if node in visited:
        return
    visited.add(node)
    print(node, end=' ')
    for neighbor in graph[node]:
        dfs(neighbor)
```



调用 `dfs('A')` 输出一种可能：`A B D E F C`

### 3.2 非递归实现（显式栈）

递归在数据规模过大时可能导致栈溢出，且有些情形需要精细控制回溯过程，这时可以手动维护一个栈：

python

```python
def dfs_iterative(start):
    visited = set()
    stack = [start]
    while stack:
        node = stack.pop()      # 弹出栈顶
        if node not in visited:
            visited.add(node)
            print(node, end=' ')
            # 注意：邻接点压栈顺序会反序，可颠倒列表保持和递归一致
            for neighbor in reversed(graph[node]):
                if neighbor not in visited:
                    stack.append(neighbor)
```



**栈的原理**：后进先出，保证每到一个节点，立刻深入它的一个邻居，直到无路可走才回溯。

### 3.3 适用场景

- 检测图中是否存在环
- 计算连通分量
- 拓扑排序（DFS后序逆序）
- 找桥或割点（Tarjan算法进阶）

------

## 四、DFS的复杂度

| 情况         | 时间复杂度            | 空间复杂度                        |
| :----------- | :-------------------- | :-------------------------------- |
| 树           | O(n) 每个节点访问一次 | O(h) h为树高，最坏O(n)            |
| 图（邻接表） | O(V+E)                | O(V) 递归栈或显式栈 + visited数组 |

------

## 五、DFS与回溯算法

DFS是“回溯”的实现手段。回溯算法可以看作**在DFS的过程中，做了选择之后向下探索，若发现不满足条件，则撤销当前选择并返回上一状态**，尝试其它可能。

**通用框架**（以全排列为例）：

python

```python
def backtrack(路径, 选择列表):
    if 满足结束条件:
        记录结果
        return
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 新的选择列表)
        撤销选择
```



**全排列问题**：

> 给定无重复数字的数组 nums，返回所有可能的排列。

python

```python
def permute(nums):
    res = []
    used = [False] * len(nums)

    def dfs(path):
        if len(path) == len(nums):
            res.append(path[:])  # 深拷贝
            return
        for i in range(len(nums)):
            if not used[i]:
                used[i] = True
                path.append(nums[i])
                dfs(path)
                path.pop()       # 撤销
                used[i] = False  # 撤销

    dfs([])
    return res
```



**这里的DFS本质是一个决策树的前序遍历**。每一次深入对应做出一个选择，递归返回对应撤销选择。排列数、组合数、子集、N皇后、解数独等问题都是这一模式。

------

## 六、DFS与剪枝优化

裸DFS容易导致指数级爆炸，必须用**剪枝**剔除不可能产生解的分支。

**常见剪枝方法：**

1. **可行性剪枝**：当前状态明显无法满足目标条件，立即返回。
   例：N皇后中，新皇后和已有的冲突，直接跳过。
2. **最优性剪枝**：求最优解时，如果当前代价已超过已知最优解，不再深入。
   例：最短路径DFS（通常BFS更优，但有些加权情况DFS+剪枝可用）。
3. **重复状态剪枝**：用备忘录或哈希表记录已计算状态（记忆化搜索）。
   例：爬楼梯、不同路径II等动态规划问题用DFS+备忘录可以达到DP效果。
4. **顺序剪枝**：通过排序等手段，让搜索尽早命中目标或尽早触发剪枝。
   例：组合求和问题中，先对候选数据排序，当当前和超过target时break循环。

**记忆化搜索示例（斐波那契）**：

python

```python
memo = {}
def fib(n):
    if n <= 1: return n
    if n not in memo:
        memo[n] = fib(n-1) + fib(n-2)
    return memo[n]
```



这保留了DFS自顶向下的思考模式，同时避免重复计算。

------

## 七、DFS与BFS的对比

| 特性       | DFS                              | BFS                          |
| :--------- | :------------------------------- | :--------------------------- |
| 数据结构   | 栈（递归或显式）                 | 队列                         |
| 空间复杂度 | O(V) 但树形结构可O(h)            | O(V) 每层宽度可能很大        |
| 找到的路径 | 不一定最短                       | 无权图必最短                 |
| 适用场景   | 回溯、拓扑排序、连通性、游戏解谜 | 最短路径、层级遍历、扩散问题 |
| 递归友好度 | 极高，代码简洁                   | 递归不自然，多以迭代实现     |

**何时选DFS？**

- 需要遍历所有可能解（排列、组合）
- 问题具有天然递归结构
- 需要回溯和撤销操作
- 图的深度远大于宽度（节省空间）

------

## 八、进阶应用：图中找所有路径、拓扑排序、强连通分量

### 8.1 图中找出所有从起点到终点的路径

在DFS回溯框架上加标记与撤销即可：

python

```python
def all_paths(graph, start, end):
    res = []
    def dfs(node, path, visited):
        if node == end:
            res.append(path[:])
            return
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                path.append(neighbor)
                dfs(neighbor, path, visited)
                path.pop()
                visited.remove(neighbor)  # 回溯撤销
    dfs(start, [start], {start})
    return res
```



注意visited要在回溯时移除，否则无法复用节点（除非节点可重复使用）。

### 8.2 拓扑排序（DFS实现）

对于有向无环图（DAG），可以利用DFS后序遍历的逆序得到拓扑排序：

python

```python
def topological_sort(graph):
    visited = set()
    stack = []
    def dfs(v):
        visited.add(v)
        for neighbor in graph[v]:
            if neighbor not in visited:
                dfs(neighbor)
        stack.append(v)  # 后序加入
    for v in graph:
        if v not in visited:
            dfs(v)
    return stack[::-1]   # 逆序即为拓扑序
```



### 8.3 强连通分量 & Tarjan算法

Tarjan算法用一个栈和DFS序（dfn/low）来计算有向图的强连通分量，它是DFS在复杂图论问题上的典范。核心是理解**追溯值low**的概念——节点能回溯到的最早祖先。这超出了入门范围，但从这里你可以窥见DFS在工业算法中的深度。

------

## 九、实战技巧与易错点

1. **递归深度限制**：Python默认递归深度约1000层，可用`sys.setrecursionlimit(1000000)`调整，但图或树极深时，最好改为手动栈。

2. **回溯时状态恢复**：路径、visited集合、临时数组，一定要成对修改和恢复，否则会污染其他分支。

3. **剪枝次序**：尽可能把剪枝条件放在递归入口处，能避免大量无效调用。

4. **避免全局变量污染**：在类或封装函数内使用局部变量加闭包传递。

5. **无向图双向边处理**：遍历时通常传入父节点参数，防止直接返回父亲形成假性环：

   python

   ```python
   def dfs(node, parent):
       for nei in graph[node]:
           if nei != parent:
               dfs(nei, node)
   ```

   

------

## 十、经典题目推荐（由浅入深）

- **树**：LeetCode 144/94/145 二叉树前中后序遍历
- **图连通性**：LeetCode 200 岛屿数量（二维网格DFS）
- **回溯**：LeetCode 46 全排列、78 子集、39 组合总和
- **剪枝优化**：LeetCode 51 N皇后、37 解数独
- **记忆化**：LeetCode 139 单词拆分、329 矩阵中最长递增路径
- **拓扑排序**：LeetCode 207 课程表（也可BFS）、210 课程表II

动手实现这些题目，每一步都自己写一遍递归和手动栈版本，会对DFS有切身体悟。

------

## 总结

- DFS本质是以**栈**驱动的纵深遍历，天然适合递归实现。
- 树的前中后序遍历是DFS的最小可运行原型。
- 图遍历需要visited防止循环。
- 回溯 = DFS + 状态恢复，解决“尝试所有可能”的问题。
- 剪枝和记忆化是DFS的优化灵魂，把指数级复杂度降到可接受范围。
- 和BFS分工不同，理解它们的时空特性和适用场景。