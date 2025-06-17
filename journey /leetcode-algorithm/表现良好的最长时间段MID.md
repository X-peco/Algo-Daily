# ⏱️ [表现良好最长时间段MID](https://leetcode.cn/problems/longest-well-performing-interval)

## 📋 题目概述

每天表现良好的评判依据是：当天工作＞8小时。求一个时间段，要求：表现良好天数＞表现非良好天数，且该时间段最长。

---

### 输出示例

>**示例 1：**  
>**输入：** `hours = [9,9,6,0,6,6,9]`  
>**输出：** `3`  
>**解释：** 最长的表现良好时间段是 `[9,9,6]`。

>**示例 2：**  
>**输入：** `hours = [6,6,6]`  
>**输出：** `0`

---

## 🤔 思考

首先我们要把条件转化为可以用来衡量的量（这个量需要能够清晰反映题目要求），也就是将数据赋予价值。  
这里我的做法是将＞8的工作时间赋值为1，≤8则是-1。  
那我们通过计算这一段时间内最终数值，如果=0则说明良好天数等于非良好，>0则是表现良好天数>表现非良好。  
这里我们利用前缀和，巧妙实现通过两个下标检测是否符合条件，且知道这个时间段长度。

---

### 🧠 思维链

1. **数据转换**  
    - **>8小时** 的工作日 → **+1**（表现良好）  
    - **≤8小时** 的工作日 → **-1**（表现非良好）
2. **前缀和计算**  
    - 计算每天的累计值（前缀和），用于快速判断任意区间的表现。  
    - **前缀和公式**： Si = S(i−1) + ai (ai=第i天的赋值值)
    ```
    下标:    0     1      2     3     4     5     6
    赋值值:        -1     1    -1     1     1     1 
    前缀和:   0    -1     0    -1     0     1     2
    ```
3. **判断规则**  
    - Sj − Si = 0 → 区间 `[i+1, j]` 内 **良好天数 = 非良好天数**
    - Sj − Si > 0 → 区间 `[i+1, j]` 内 **良好天数 > 非良好天数**
4. **示例**  
    - **前缀和数组**： `[0, -1, 0, -1, 0, 1, 2]`
    - **区间 [2, 4]**（天数2~4）：S4 − S1 = 0 − (−1) = 1 (良好天数多1天)

---

## 🛠️ 前缀和的实现

<details>
  <summary><strong>Python 代码</strong></summary>

```python
class Solution(object):
    def longestWPI(self, hours):
        n = len(hours)
        # 构造前缀和，若 hours[i] > 8 则记为 +1，否则 -1
        prefix = [0] * (n + 1)
        for i in range(n):
            prefix[i+1] = prefix[i] + (1 if hours[i] > 8 else -1)
```
</details>

<details>
  <summary><strong>C++ 代码</strong></summary>

```cpp
class Solution {
public:
    int longestWPI(vector<int>& hours) {
        int n = hours.size();
        // 构建前缀和数组，prefix[0] = 0
        vector<int> prefix(n + 1, 0);
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] + (hours[i] > 8 ? 1 : -1);
        }
    }
};
```
</details>

---

## 🚀 优化

得出前缀和后，我们只需挨个配对就好了，但此时的时间复杂度就是 n + (n-1) + ... + 1，为 O(n²)。  
那我们思考一下还可以从哪些角度出发去优化。  
现在我们将问题简化为了，通过前后两个下标 j 和 i，可以同时获得时间段长度 (i-j)，并检查是否符合条件 (prefix[i]-prefix[j]>0)。  
我们不妨想象，对于每个左下标 j，要是有 j1<j2, 且 prefix[j1]<prefix[j2]，要是在二者中择取左下标，是不是 prefix[j1] 的门槛更低（prefix[i]-prefix[j1]>0），且 i-j1 更长，这样看，j2 便可以舍去了。  
而对于 j1<j2, 且 prefix[j1]>prefix[j2]，则可以保留 j2。  
对于右节点，我们则从后往前遍历即可实现尽可能找到最长时间段。

---

### 🏗️ 下标择取优化

<details>
  <summary><strong>Python 代码</strong></summary>

```python
class Solution(object):
    def longestWPI(self, hours):
        n = len(hours)
        # 构造前缀和，若 hours[i] > 8 则记为 +1，否则 -1
        prefix = [0] * (n + 1)
        for i in range(n):
            prefix[i+1] = prefix[i] + (1 if hours[i] > 8 else -1)
        
        # 构造单调栈
        stack = []
        for i in range(n+1):
            if not stack or prefix[i] < prefix[stack[-1]]:
                stack.append(i)
        
        max_length = 0
        # 从后往前遍历寻找最大长度区间
        for j in range(n, -1, -1):
            while stack and j > stack[-1] and prefix[j] > prefix[stack[-1]]:
                max_length = max(max_length, j - stack.pop())
        return max_length
```
</details>

<details>
  <summary><strong>C++ 代码</strong></summary>

```cpp
class Solution {
public:
    int longestWPI(vector<int> &hours) {
        int n = hours.size();
        // 构建前缀和数组，prefix[0] = 0
        vector<int> prefix(n + 1, 0);
        for (int i = 0; i < n; i++) {
            prefix[i + 1] = prefix[i] + (hours[i] > 8 ? 1 : -1);
        }
        // 单调递减栈，保存前缀和的索引
        stack<int> mono;
        for (int i = 0; i < prefix.size(); i++) {
            if (mono.empty() || prefix[i] < prefix[mono.top()]) {
                mono.push(i);
            }
        }
        int maxLength = 0;
        // 从后往前遍历，找最长区间
        for (int j = prefix.size() - 1; j >= 0; j--) {
            while (!mono.empty() && prefix[j] > prefix[mono.top()]) {
                maxLength = max(maxLength, j - mono.top());
                mono.pop();
            }
        }
        return maxLength;
    }
};
```
</details>

---

## ⏳ 时间复杂度 O(n)

- 每个点不会重复遍历，效率极高。

---
