# [最短桥MID](https://leetcode.cn/problems/shortest-bridge)

## 题目描述 🚀
> **给定一个矩阵，其中1表示地面，0表示海洋。已知一定有两片岛屿，求两片岛屿之间最短距离**

## 思路 ♾️
**首先肯定是需要找出岛屿范围，我们可以遍历每个点检测是否找到岛屿，然后用DFS标记这个岛屿的边界(0点)**


<details>
  <summary><strong>🐍Python</strong></summary>

```python
class Solution(object):
    def shortestBridge(self, grid):

        rows = len(grid)
        cols = len(grid[0])
        queue = deque()
        direction = [(-1,0), (1,0), (0,1), (0,-1)]

        def dfs(row, col):

            # 边界检查
            if row < 0 or row >= rows or col < 0 or col >= cols:
                return

            # 如果不是陆地，且遇到海洋时加入队列，直接返回
            if grid[row][col] != 1:
                if grid[row][col] == 0:
                    queue.append((row, col))
                return

            grid[row][col] = 2  # 标记已访问

            for dx, dy in direction:
            
                new_row = row + dy  # dy用在行上
                new_col = col + dx  # dx用在列上
                dfs(new_row, new_col)#递归

        # 找到第一个岛屿并进行深度优先搜索

        found = False#条件锚点

        for i in range(rows):
        
            if found:
                break

            for j in range(cols):
                if grid[i][j] == 1:#已找到第一个岛屿
                    dfs(i, j)
                    found = True#更新锚点
                    break
```

</details>

<details>
  <summary><strong>🦕C++ </strong></summary>

```c++
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int shortestBridge(vector<vector<int>>& grid) {
        int rows = grid.size(), cols = grid[0].size();
        queue<pair<int, int>> q;
        bool found = false;
        // 使用 lambda 函数实现 DFS，标记第一个岛屿，同时将边界海洋点加入队列 📌
        auto dfs = [&](auto &self, int r, int c) -> void {
            if (r < 0 || r >= rows || c < 0 || c >= cols)
                return;
            if (grid[r][c] != 1) {
                if (grid[r][c] == 0)
                    q.push({r, c});
                return;
            }
            grid[r][c] = 2;  // 标记已访问✅
            int dr[4] = {-1, 1, 0, 0};
            int dc[4] = {0, 0, 1, -1};
            for (int i = 0; i < 4; i++) {
                self(self, r + dr[i], c + dc[i]);
            }
        };
        
        // 找到第一个岛屿进行 DFS 标记
        for (int i = 0; i < rows && !found; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 1) {
                    dfs(dfs, i, j);
                    found = true;
                    break;
                }
            }
        }
```

</details>

**至此我们就可以描述出一个岛屿的范围了**

**接下来的目标是计数，从第一个岛屿边界到第二个岛屿距离，最短距离**

**这里自然不可以对每个边界点进行DFS直到找到第二个岛屿，因为得出的长度是随机走向，满足不了寻求最短距离条件**

**对此我们采用BFS，如何用呢，规则是对当前所有边界点，进行向四个邻位查找的操作，即实现每次是移动一位，如果邻位是海洋则将邻位，添加跟新进边界集**

**注意到，这里我们需要在每个边界点向邻伸展后，才对新的边界点集重复这样的操作，故我们使用==队列==这个数据结构，并实时标记当前边界点数量，确保在当前边界点全部操作完了，才对下一个边界点集进行同样的操作**

## BFS 寻找第二个岛屿的代码示例


<details>
  <summary><strong>🐍Python </strong></summary>

```python
level = 0#桥长度，也可以说是BFS深度
        # 使用BFS逐层扩展寻找第二个岛
        while queue:

            length = len(queue)#计数当前边界点数量
            level += 1

            for _ in range(length):

                row, col = queue.popleft()
                for dx, dy in direction:

                    new_row = row + dy
                    new_col = col + dx
                    # 检查边界
                    if new_row < 0 or new_row >= rows or new_col < 0 or new_col >= cols:

                        continue

                    if grid[new_row][new_col] == 1:#找到第二个岛屿
                        return level

                    elif grid[new_row][new_col] == 0:#如果是海洋 
                        grid[new_row][new_col] = 2   #标记已访问
                        queue.append((new_row, new_col))#添加，更新入新的边界集

        return level
```

</details>

<details>
  <summary><strong>🦕C++</strong></summary>

```c++
// 使用 BFS 从边界海洋点逐层扩展，寻找第二个岛屿
int level = 0;
int dr[4] = {-1, 1, 0, 0};
int dc[4] = {0, 0, 1, -1};
while (!q.empty()) {
    int len = q.size();
    level++;
    for (int i = 0; i < len; i++) {
        auto cur = q.front();
        q.pop();
        int r = cur.first, c = cur.second;
        for (int d = 0; d < 4; d++) {
            int new_r = r + dr[d];
            int new_c = c + dc[d];
            if (new_r < 0 || new_r >= rows || new_c < 0 || new_c >= cols)
                continue;
            if (grid[new_r][new_c] == 1)
                return level;
            if (grid[new_r][new_c] == 0) {
                grid[new_r][new_c] = 2;
                q.push({new_r, new_c});
            }
        }
    }
}
        
return level;
```

</details>

## 时间复杂度 ⏱️
- *O(m×n)，其中 m 和 n 为矩阵的长宽* 
- *在最坏情况下，需要操作矩阵中所有的点，但实际情况通常小于 O(m×n)👍*

