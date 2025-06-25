# [**矩阵距0最小距离MID**](https://leetcode.cn/problems/01-matrix)

## 题目描述

>*给定一个由 0 和 1 组成的矩阵 mat ，请输出一个大小相同的矩阵，其中每一个格子是 mat 中对应位置元素到最近的 0 的距离。*
>
>*两个相邻元素间的距离为 1。*

---

### 输出示例

>示例 1：
>![image](https://github.com/user-attachments/assets/613f00f4-2838-46b1-bae9-6ec83e00eaff)
>
>输入：
```
mat = [[0,0,0],
       [0,1,0],
       [0,0,0]]
```
>输出：
```
[[0,0,0],
 [0,1,0],
 [0,0,0]]
```
>示例 2：
>
>![image](https://github.com/user-attachments/assets/f2dd55ff-c3fc-4cb2-9aaa-dab49ba69e2e)
>
>输入：
```
mat = [[0,0,0],
       [0,1,0],
       [1,1,1]]
```
>输出：
```
[[0,0,0],
 [0,1,0],
 [1,2,1]]
```

---

## 思路

**本题等价于求每个 1 到最近 0 的距离。可以用 BFS 或 动态规划（DP）实现。**

**用BFS则每个元素查找更新自己的外层，可以用个状态表记录访问过的地方，但时间复杂度为o(n*n*m*m)**

**用动态规划的话，先创建二位数组DP，状态转移方程式为，当前最小距离=（上下左右）中最小距离+1，但是从何处作为边界传播状态呢，边界的话你也设置不了初始状态，我们就设置初始每个点的距离无穷大，先找每个点（上左）的最小，从头遍历到尾，再反过来做一次**

这里介绍 **两次动态规划** 的经典做法：

- 首次从左上到右下，更新上和左的信息
- 再次从右下到左上，更新下和右的信息
- 保证四个方向都被扫描到

---

## 🐍 Python 

```python
from typing import List

class Solution:
    def updateMatrix(self, mat: List[List[int]]) -> List[List[int]]:
        if not mat: return []
        n, m = len(mat), len(mat[0])
        dp = [[float('inf')] * m for _ in range(n)]
        # 第一次：左上到右下
        for i in range(n):
            for j in range(m):
                if mat[i][j] == 0:
                    dp[i][j] = 0
                else:
                    if i > 0:
                        dp[i][j] = min(dp[i][j], dp[i-1][j] + 1)
                    if j > 0:
                        dp[i][j] = min(dp[i][j], dp[i][j-1] + 1)
        # 第二次：右下到左上
        for i in range(n-1, -1, -1):
            for j in range(m-1, -1, -1):
                if mat[i][j] != 0:
                    if i < n-1:
                        dp[i][j] = min(dp[i][j], dp[i+1][j] + 1)
                    if j < m-1:
                        dp[i][j] = min(dp[i][j], dp[i][j+1] + 1)
        return dp
```

---

## 🦖 C++ 

```cpp
class Solution {
public:
    vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
        if (mat.empty()) return {};
        int n = mat.size(), m = mat[0].size();
        vector<vector<int>> dp(n, vector<int>(m, INT_MAX - 1));
        // 第一次：左上到右下
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                if (mat[i][j] == 0) {
                    dp[i][j] = 0;
                } else {
                    if (i > 0) dp[i][j] = min(dp[i][j], dp[i-1][j] + 1);
                    if (j > 0) dp[i][j] = min(dp[i][j], dp[i][j-1] + 1);
                }
            }
        }
        // 第二次：右下到左上
        for (int i = n-1; i >= 0; --i) {
            for (int j = m-1; j >= 0; --j) {
                if (mat[i][j] != 0) {
                    if (i < n-1) dp[i][j] = min(dp[i][j], dp[i+1][j] + 1);
                    if (j < m-1) dp[i][j] = min(dp[i][j], dp[i][j+1] + 1);
                }
            }
        }
        return dp;
    }
};
```

---

## 总结

- 两次动态规划，四个方向全部覆盖
- 初始化为无穷大，注意防止溢出
- 也可以用 BFS 多源扩展实现

