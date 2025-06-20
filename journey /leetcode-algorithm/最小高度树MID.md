# 🌳 [最小高度树MID](https://leetcode.cn/problems/minimum-height-trees)

# 📋 题目描述

**给你一棵树（没有环路、连通的无向图），有 n 个节点（编号从 0 到 n-1），以及包含 n-1 条边（表示两个节点相连）的列表。你可以任选一个节点作为树的根节点。  
树的高度指的是：根节点到最远的叶子节点经过的边数**

**你需要做的是：**
- ***找出所有可以当根节点、并且让树的高度**最小**的节点编号。
- ***返回这些节点的编号（顺序随意）。***


# 💡 示例
>**示例 1：**
![](https://assets.leetcode.com/uploads/2020/09/01/e1.jpg)
>
>**输入：n = 4, edges = [[1,0],[1,2],[1,3]]**
>**输出：**[1]
>**解释：如图所示，当根是标签为 1 的节点时，树的高度是 1 ，这是唯一的最小高度树。**

>**示例 2：**
![](https://assets.leetcode.com/uploads/2020/09/01/e2.jpg)
>
>**输入：n = 6, edges = [[3,0],[3,1],[3,2],[3,4],[5,4]]**
>**输出：**[3,4]

---

# 🤔 思考

**首先给的列表包含节点的连接关系，即树的框架，找最小高度树，即找中心节点，何为中心节点，我们知道树可以从任意一点出发，向外发散直到叶子，这些叶子，即只有一个连接，处于树的最边缘，倘若我们像剥洋葱一样，不断裁剪最外层的叶子，到剩下的就只有中心节点了，我们想象一下裁剪到最后一步，我们会剩下几个中心节点**
>1.A-b-C       2.A-b-c-D
>
*第一类情况裁剪后只剩下b*，**一个**

*第二类裁剪后只剩下bc* ，**两个**

*可不可以有第三个呢，那我们只能把第三个假设的中心节点放在b或c上，但此时中心节点会被视为叶子被裁剪，所以中心节点至多有两个*

---

**给的列表是节点连接的关系，我们做个列表，存储n个集合，列表下标对应的集合即是存储对应节点的邻节点**
#### 🏗️ 构造邻接表

<details>
<summary>Python </summary>

```python
neighbors = [set() for _ in range(n)]

for x, y in edges:
    neighbors[x].add(y)
    neighbors[y].add(x)
```
</details>

<details>
<summary>C++ </summary>

```cpp
vector<unordered_set<int>> neighbors(n);
for (const auto& edge : edges) {
    int x = edge[0], y = edge[1];
    neighbors[x].insert(y);
    neighbors[y].insert(x);
}
```
</details>

#### 🌱 初始化标记叶子

<details>
<summary>Python </summary>

```python
leaves = [i for i in range(n) if len(neighbors[i]) == 1]
```
</details>

<details>
<summary>C++ </summary>

```cpp
vector<int> leaves;
for (int i = 0; i < n; ++i) {
    if (neighbors[i].size() == 1) {
        leaves.push_back(i);
    }
}
```
</details>


#### 🔄 全流程+更新叶子

<details>
<summary>Python 实现</summary>

```python
class Solution(object):
    def findMinHeightTrees(self, n, edges):
        """
        :type n: int
        :type edges: List[List[int]]
        :rtype: List[int]
        """
        if n == 1:
            return [0]

        neighbors = [set() for _ in range(n)]

        for x, y in edges:
            neighbors[x].add(y)
            neighbors[y].add(x)

        leaves = [i for i in range(n) if len(neighbors[i]) == 1]

        rest_nodes = n
        while rest_nodes > 2:
            rest_nodes -= len(leaves)
            new_leaves = []
            for leave in leaves:
                next_node = neighbors[leave].pop()
                neighbors[next_node].remove(leave)
                if len(neighbors[next_node]) == 1:
                    new_leaves.append(next_node)
            leaves = new_leaves

        return leaves
```
</details>

<details>
<summary>C++ 实现</summary>

```cpp
class Solution {
public:
    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        if (n == 1) return {0};

        vector<unordered_set<int>> neighbors(n);
        for (const auto& edge : edges) {
            int x = edge[0], y = edge[1];
            neighbors[x].insert(y);
            neighbors[y].insert(x);
        }

        vector<int> leaves;
        for (int i = 0; i < n; ++i) {
            if (neighbors[i].size() == 1) leaves.push_back(i);
        }

        int rest_nodes = n;
        while (rest_nodes > 2) {
            rest_nodes -= leaves.size();
            vector<int> new_leaves;
            for (int leave : leaves) {
                int next_node = *neighbors[leave].begin();
                neighbors[next_node].erase(leave);
                if (neighbors[next_node].size() == 1) {
                    new_leaves.push_back(next_node);
                }
            }
            leaves = new_leaves;
        }

        return leaves;
    }
};
```
</details>



# ⏱️ 时间复杂度
* o(n)
* 每个节点操作一次
