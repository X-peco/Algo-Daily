# [最小路径和MID](https://leetcode.cn/problems/minimum-path-sum)

# 📑题目描述

>**给定一个包含非负整数的 `_m_ x _n_` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。**
>
>**说明：每次只能向下或者向右移动一步。**

# 🙊输出示例
>**示例 1：**
>![](https://assets.leetcode.com/uploads/2020/11/05/minpath.jpg)
>
>**输入：grid** = [[1,3,1],[1,5,1],[4,2,1]]
>
>**输出：7**
>**解释：因为路径 1→3→1→1→1 的总和最小。**

>**示例 2：**
**输入：grid = [[1,2,3],[4,5,6]]**
>
>**输出：12**


# 💭思考
**采用动态规划，首先创建一个二位数组lib，用来保存走到对应点时最小路径
每个对应点的状态转换方程式为lib\[row]\[col]=min(lib\[row]\[col-1],lib]\[row-1]\[col])+grid\[row]\[col]
即左边与上面之间的最小+当前值->grid\[row]\[col]**

---
# 示例代码

<details>
  <summary>🦖C++ </summary>

```cpp
int minPathSum(vector<vector<int>>& grid) {
    int rows = grid.size(), cols = grid[0].size();
    vector<int> former(cols, 0);
    for (int row = 0; row < rows; row++){
        for (int col = 0; col < cols; col++){
            if (row == 0 && col == 0) {
                former[col] = grid[row][col];
            }
            else if (col == 0) {
                former[col] = former[col] + grid[row][col];
            }
            else if (row == 0) {
                former[col] = former[col - 1] + grid[row][col];
            }
            else {
                former[col] = min(former[col], former[col - 1]) + grid[row][col];
            }
        }
    }
    return former[cols - 1];
}
```
</details>


<details>
  <summary>🐍 Python</summary>

```python
from typing import List

def minPathSum(grid: List[List[int]]) -> int:
    rows, cols = len(grid), len(grid[0])
    former = [0] * cols
    for row in range(rows):
        for col in range(cols):
            if row == 0 and col == 0:
                former[col] = grid[row][col]
            elif row == 0:
                former[col] = former[col - 1] + grid[row][col]
            elif col == 0:
                former[col] += grid[row][col]
            else:
                former[col] = min(former[col], former[col - 1]) + grid[row][col]
    return former[cols - 1]
```
</details>

---
# 💯优化

**我们可以将lib缩短为一行，修改lib\[col]为min(lib\[col-1](前一个)，lib\[col](此时还未重新赋值，仍表示头顶)+grid\[row]\[col]


<details>
  <summary>🦖C++ </summary>

```cpp
int minPathSum(vector<vector<int>>& grid) {
    int m = grid.size(), n = grid[0].size();
    vector<int> dp(n, 0);
    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            if (i == 0 && j == 0) {
                dp[j] = grid[i][j];
            } else if (i == 0) {
                dp[j] = dp[j - 1] + grid[i][j];
            } else if (j == 0) {
                dp[j] = dp[j] + grid[i][j];
            } else {
                dp[j] = min(dp[j], dp[j - 1]) + grid[i][j];
            }
        }
    }
    return dp[n - 1];
}
```
</details>

<details>
  <summary>🐍Python</summary>

```python
from typing import List

def minPathSum(grid: List[List[int]]) -> int:
    m, n = len(grid), len(grid[0])
    dp = [0] * n
    for i in range(m):
        for j in range(n):
            if i == 0 and j == 0:
                dp[j] = grid[i][j]
            elif i == 0:
                dp[j] = dp[j - 1] + grid[i][j]
            elif j == 0:
                dp[j] = dp[j] + grid[i][j]
            else:
                dp[j] = min(dp[j], dp[j - 1]) + grid[i][j]
    return dp[n - 1]

# 示例测试
if __name__ == '__main__':
    grid = [
        [1, 3, 1],
        [1, 5, 1],
        [4, 2, 1]
    ]
    print(minPathSum(grid))  # 输出 7
```
</details>

---

# ⏱️复杂度
* 两者时间复杂度都为o(n)
* 优化后空间复杂度缩小了rows倍


---

# 彩蛋？与GPT的探讨

#ME：
>*动态规划和递归的联系，我发现两者很核心的一个点是，{信任}，动态规划中是需要用”相邻，前一个成功结果推及下一个结果“，递归中是自己调用自己。二者都是将复杂思想抽象为小的动作，相信每个小动作愈演愈烈，并完成目的。还有动态规划中要设置初始正确结果，递归则是设置停止条件*


#GPT：
***你说得很对，动态规划（DP）和递归这两者之间确实有着密切的联系，核心在于利用“信任”的思想。***
1. **基本思想**：
    
    - *在递归中，我们将整个问题分解成一些更小的、相似的子问题，然后让函数自己调用自己。核心在于信任递归调用返回的结果是正确的，只要递归的终止条件设置得当，最终就能组合出问题的答案。*
    - *而在动态规划中，尤其是当我们使用自顶向下的方式（带备忘录的递归）时，本质上还是在进行递归，并且我们通过缓存中间结果来避免重复计算。另一方面，动态规划也可以用自底向上的迭代方式实现，这时我们“依赖”于先解决子问题，然后将这些结果用于构建更大的问题的答案。*
2. **“信任”的核心：**
    
    - *无论是递归还是动态规划，都需要设定一个基准，也就是递归中的停止条件或动态规划中的初始条件。这些条件确保了递归不会无限调用，也保证了在递归或迭代过程中，每一步推导都是建立在前一个已验证的正确结果基础之上。*
3. **小动作的叠加：**
    
    - *都把复杂的问题拆解为一系列相对简单的小步骤。通过逐步推导或者递归调用，每个小步骤在彼此“信任”的基础上累积起来，最终完成整个问题的求解。*

*总的来说，动态规划可以看做是一种利用递归思想解决问题的方法，只不过它在过程中增加了记忆化或迭代求解，来减少重复计算，从而更加高效。两者的共同点在于都需要良好的问题分解、设定明确的初始条件（或边界条件），并且通过简单动作累积构建出更复杂的解。*
