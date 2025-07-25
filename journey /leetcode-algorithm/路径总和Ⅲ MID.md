#  [🌳 路径总和Ⅲ MID](https://leetcode.cn/problems/path-sum-iii)

## 📝 题目描述

> **给定一个二叉树的根节点 `root` 和一个整数目标和 `targetSum`，统计二叉树里路径和等于目标和的路径的数目。**
>
> 路径不需要从根节点开始，也不需要在叶子节点结束，但路径方向必须是向下的（只能从父节点到子节点）。

### 🧩 示例输出

> **示例 1：**

![](https://assets.leetcode.com/uploads/2021/04/09/pathsum3-1-tree.jpg)

>**输入：root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8**
>
**输出：3**
>
**解释：和等于 8 的路径有 3 条，如图所示**

>**示例 2：**
>
**输入：root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22**
>
**输出：3**

---

## 💡 思路

**本题的核心是：用前缀和 + 哈希表统计路径和。**

- 和一样，可以[***和为k的子数组***](https://github.com/X-peco/Algo-Daily/blob/main/journey%20/leetcode-algorithm/%E5%92%8C%E4%B8%BAk%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84MID.md)用前缀和思想，但变成了树上的路径。
- 递归遍历树，维护一条从根到当前节点的路径的前缀和 pre_sum
- 用哈希表 `mp` 记录「某个前缀和 pre_sum 出现的次数」
- 到达当前节点时，若存在 pre_sum - targetSum，则说明从某个祖先节点到当前节点的路径和为 targetSum
- 每次递归返回后，要回溯（即 mp[pre_sum] -= 1），以免影响到兄弟分支

### 🌳 前缀和在树上的应用

| 路径                     | 节点值之和 | 说明                  |
| ------------------------ | ---------- | --------------------- |
| 根 -> ... -> 当前节点     | pre_sum    | 当前路径前缀和        |
| 某祖先 -> ... -> 当前节点 | pre_sum - old_sum = targetSum | 祖先节点 old_sum        |

**初始化同样需要 `mp[0] = 1`，表示从根节点出发、路径和等于 targetSum 的情况。**

---

## 🧭 前缀和 + 哈希表 + DFS

<details>
  <summary><strong>🐍 Python</strong></summary>

```python
class Solution:
    def pathSum(self, root, targetSum):
        from collections import defaultdict
        mp = defaultdict(int)
        mp[0] = 1

        def dfs(node, pre_sum):
            if not node:
                return 0
            pre_sum += node.val
            res = mp[pre_sum - targetSum]
            mp[pre_sum] += 1
            res += dfs(node.left, pre_sum)
            res += dfs(node.right, pre_sum)
            mp[pre_sum] -= 1  # 回溯
            return res

        return dfs(root, 0)
```
</details>

<details>
  <summary><strong>🦕 C++</strong></summary>

```cpp
class Solution {
public:
    int pathSum(TreeNode* root, int targetSum) {
        unordered_map<long long, int> mp;
        mp[0] = 1;
        function<int(TreeNode*, long long)> dfs = [&](TreeNode* node, long long pre_sum) -> int {
            if (!node) return 0;
            pre_sum += node->val;
            int res = mp[pre_sum - targetSum];
            mp[pre_sum]++;
            res += dfs(node->left, pre_sum);
            res += dfs(node->right, pre_sum);
            mp[pre_sum]--;
            return res;
        };
        return dfs(root, 0);
    }
};
```
</details>

---

## ⏱️ 复杂度分析

- *时间复杂度：O(N)，N 为节点数，每个节点只遍历一次*

## ♾️DFS再思考
*DFS不需要每层返回，别看没有显式往上层传递信息，但是它每层结尾都会消除走过的痕迹*

`mp[pre_sum] -= 1`
`pre_sum -= node.val`

*这两部只有当当前路径走到头了，分支也走到头了，才会从尾巴开始删除*

---
