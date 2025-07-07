# [✨ 和为 K 的子数组 MID](https://leetcode.cn/problems/subarray-sum-equals-k)


## 📝 题目描述

> **给你一个整数数组 `nums` 和一个整数 `k`，请你统计并返回该数组中和为 `k` 的子数组的个数。**
>
> 子数组是数组中元素的连续非空序列。

### 🧩 示例输出

> **示例 1：**
>
> **输入：nums = [1,1,1], k = 2**  
> **输出：2**
>
> **示例 2：**
>
> **输入：nums = [1,2,3], k = 3  **
> **输出：2**

---

## 💡 思路

**这题考查前缀和与哈希表结合的思想。**

- 遍历数组时，用一个变量 `pre_sum` 记录前缀和
- 用哈希表 `mp` 作为锚点，记录每个前缀和出现的次数
- 对于当前位置 ，当前前缀和为 `pre_sum`，如果存在先前某个位置的前缀和为 `pre_sum - k`，那么存在区间长度为k
- 所以，每次遇到 `pre_sum - k`，就把它出现的次数加到答案里

### 🌲前缀和

| 下标i/j  | 0   | 1   | 2   | 3     |
| ------ | --- | --- | --- | ----- |
| 前缀和s   | 0   | A   | A+B | A+B+C |
| 数组nums | -   | A   | B   | C     |


**s(i)-s(j)\==为区间(j,i]的和**

**初始化很重要：**`mp[0] = 1`，表示“从头开始的子数组和为 k”的情况。

---

## 🧭 前缀和 + 哈希表

<details>
  <summary><strong>🐍 Python </strong></summary>

```python
class Solution:
    def subarraySum(self, nums, k):
        from collections import defaultdict
        mp = defaultdict(int)
        mp[0] = 1
        pre_sum = 0
        ans = 0
        for x in nums:
            pre_sum += x
            ans += mp[pre_sum - k]
            mp[pre_sum] += 1
        return ans
```
</details>

<details>
  <summary><strong>🦕 C++ </strong></summary>

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        unordered_map<int, int> mp;
        mp[0] = 1;
        int pre_sum = 0, res = 0;
        for (int x : nums) {
            pre_sum += x;
            if (mp.count(pre_sum - k))
                res += mp[pre_sum - k];
            mp[pre_sum]++;
        }
        return res;
    }
};
```
</details>

---

## ⏱️ 复杂度分析

- 时间复杂度：O(N)，只遍历一遍数组

---
