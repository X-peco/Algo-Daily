# [ 🚦 单词接龙HARD](https://leetcode.cn/problems/word-ladder-ii)

# 题目描述

>***给你两个单词，一个单词作为起始单词，一个单词为终点单词，要求你从起始单词开始，每次修改一个字符，最少需要经历几个中间态
>同时给定一个中间态单词列表，要求修改过程产生的中间态单词，出现在这个单词列表中***


## 示例输出

>**输入：beginWord = "hit", endWord = "cog", wordList =\["hot","dot","dog","lot","log","cog"]**
>
  **输出：\[\["hit","hot","dot","dog","cog"],\["hit","hot","lot","log","cog"]]**
  
>**解释：存在 2 种最短的转换序列：**
*"hit" -> "hot" -> "dot" -> "dog" -> "cog"
"hit" -> "hot" -> "lot" -> "log" -> "cog"*

---
# 思路

**题目要求每次只能修改一个字符，同时这个字符还需要存在于中间态列表中**

**我们先想想，其中一个结果，它的*结构特征*大致是怎么样的，就给出的输出示例来看\["hit","hot","lot","log","cog"]，每个相邻单词的差异为一个字符的不同，进一步想象，每个单词视为一个*节点*，能与这个节点相连的要求是，只有*一个字符差异*，就这么连连连，直到遇到endWord截止**

**于是我们就需要，查找从beginWord开始，相邻的，且存在于*中间态单词集*的，*相邻单词集*。再对相邻单词集的单词重复同样操作，给其中的单词搜集出，相邻的，且存在于*中间态单词集*的，*相邻单词集*。**

 **我们维护一个队列A和一个集合。这个A一开始只存储单词(begin Word)，集合用来o(1)复杂度查找，储存的是*中间态列表的值*，我们还是采取BFS的策略，** ***遍历队列，查找相邻***，**接下来我们的操作需要做出两个行为，BFS的同时储存每个中间态单词的相邻单词，举个例子，我们先创建一个空队列B，从beginWord开始，先创造它所有的相邻单词，同时匹配找到出现在*中间态单词列表*中的那些，把这些单词加入beginWord的相邻集，同时这些单词加入B，由于现在只有一个单词，所以，进行的操作只有，给这一个单词查找*相邻单词*，并且B添加的只有这个单词的相邻单词，然后将A更新为B，同时将*中间态单词集*的中的B全部删除，避免下一层查找时候查找当前单词，我解释这个行为为（倒流）**



  # BFS/相邻单词集

<details>
  <summary><strong>🐍Python </strong></summary>

```python
while q1 and not found:
    # 新的层
    q = set()
    # 遍历当前队列所有单词
    for word in q1:
        for tar_index in range(len(word)):
            for ex in alpha:
                # 节省重复的小技巧
                if word[tar_index] == ex:
                    continue
                next_word = word[:tar_index] + ex + word[tar_index+1:]
                if next_word in q2:
                    found = True
                    next_words[word].append(next_word)
                if not found and next_word in dic_words:
                    next_words[word].append(next_word)
                    q.add(next_word)
    # 本层结束，删掉已访问的节点，防止倒流
    dic_words -= q
```
</details>


<details>
  <summary><strong>🦕C++</strong></summary>

```cpp
void bfsWordLadder(
    unordered_set<string>& q1,
    unordered_set<string>& q2,
    unordered_set<string>& dic_words,
    unordered_map<string, vector<string>>& next_words,
    const string& alpha,
    bool& found
) {
    while (!q1.empty() && !found) {
        unordered_set<string> q;
        for (const string& word : q1) {
            for (size_t tar_index = 0; tar_index < word.size(); ++tar_index) {
                for (char ex : alpha) {
                    if (word[tar_index] == ex) continue;
                    string next_word = word.substr(0, tar_index) + ex + word.substr(tar_index + 1);
                    if (q2.find(next_word) != q2.end()) {
                        found = true;
                        next_words[word].push_back(next_word);
                    }
                    if (!found && dic_words.find(next_word) != dic_words.end()) {
                        next_words[word].push_back(next_word);
                        q.insert(next_word);
                    }
                }
            }
        }
        for (const string& w : q) dic_words.erase(w);
        q1 = std::move(q);
    }
}
```
</details>

---

## 优化

**结合这里BFS，逐层遍历，查找相邻，的性质，观察到一个现象，从beginWord开始，假设每次有效相邻单词为n个，队列演化：1->n->n*n->n*n*n->...深度m为beginWord长度(默认beginWord和endWord长度一样)，可见这个指数变化随着BFS深度，会造成大幅的性能下滑，我们原来的方式是从beginWord开始的队列A，那我们增加一个从endWord开始的逆向的队列C，如果队列B中的单词的相邻单词出现在C中，则完成了节点的相连，实现了一个完整的路径，每次跟新B，用最短(单词数量最少)的队列(A/C)跟新，尽可能减少指数的巨额指数增加**

<details>
  <summary><strong>对变量reversed_flag的提示</strong></summary>

**reversed_flag 用来检测是从头来还是从尾巴来，以便清楚，对单词添加相邻单词的逻辑**

</details>



<details>
  <summary><strong>🐍Python</strong></summary>

```python
while q1 and not found:
    q = set()
    for word in q1:
        s = list(word)
        for i in range(len(s)):
            origin = s[i]
            for c in 'abcdefghijklmnopqrstuvwxyz':
                s[i] = c
                new_word = ''.join(s)
                if new_word in q2:
                    # 若在另一端集合中，说明找到了连接点
                    if reversed_flag:
                        next_map[new_word].append(word)
                    else:
                        next_map[word].append(new_word)
                    found = True
                if not found and new_word in dict_set:
                    if reversed_flag:
                        next_map[new_word].append(word)
                    else:
                        next_map[word].append(new_word)
                    q.add(new_word)
            s[i] = origin
    dict_set -= q
    # 优先扩展节点较少的一端
    if len(q) < len(q2):
        q1 = q
    else:
        reversed_flag = not reversed_flag
        q1, q2 = q2, q
```
</details>



<details>
  <summary><strong>🦕C++</strong></summary>

```cpp
while (!q1.empty() && !found) {
    // 新一层
    std::unordered_set<std::string> q;
    // 遍历当前层的所有节点
    for (const auto& word : q1) {
        std::string s = word;
        for (size_t i = 0; i < s.size(); ++i) {
            char origin = s[i];
            for (char c = 'a'; c <= 'z'; ++c) {
                s[i] = c;
                std::string new_word = s;
                if (q2.find(new_word) != q2.end()) {
                    // 若在另一端集合中，说明找到了连接点
                    if (reversed_flag) {
                        next_map[new_word].push_back(word);
                    } else {
                        next_map[word].push_back(new_word);
                    }
                    found = true;
                }
                if (!found && dict_set.find(new_word) != dict_set.end()) {
                    if (reversed_flag) {
                        next_map[new_word].push_back(word);
                    } else {
                        next_map[word].push_back(new_word);
                    }
                    q.insert(new_word);
                }
            }
            s[i] = origin;
        }
    }
    // 本层结束，删掉已访问的节点，防止环
    for (const auto& w : q) dict_set.erase(w);
    // 优先扩展节点较少的一端
    if (q.size() < q2.size()) {
        q1 = q;
    } else {
        reversed_flag = !reversed_flag;
        std::swap(q1, q2);
        q1 = q;
    }
}
```
</details>

---

***至此我们通过BFS，找出了beginWord的相邻单词，以及由此衍生出，相邻单词的相邻单词(似乎有点绕，其实理解了的话，还蛮好理解的？？)，于是差一个相对轻松一点的递归+回溯出答案了，递归每个单词的单词集，同时注意回溯的设置***

<details>
  <summary><strong>🐍Python</strong></summary>

```python
if found:
    path = [beginWord]
    def backtracking(src: str):
        if src == endWord:
            ans.append(list(path))
            return
        for nxt in next_map[src]:
            path.append(nxt)
            backtracking(nxt)
            path.pop()
    backtracking(beginWord)
return ans
```
</details>



<details>
  <summary><strong>🦕C++</strong></summary>

```cpp
if (found) {
    std::vector<std::string> path{beginWord};
    std::vector<std::vector<std::string>> ans;
    std::function<void(const std::string&)> backtracking = [&](const std::string& src) {
        if (src == endWord) {
            ans.push_back(path);
            return;
        }
        for (const auto& nxt : next_map[src]) {
            path.push_back(nxt);
            backtracking(nxt);
            path.pop_back();
        }
    };
    backtracking(beginWord);
    return ans;
}
```
</details>

---

# 时间复杂度

* *这一题就比较复杂了分析，但前面也稍微涉及了一点“队列演化：1->n->n*n->n*n*n->...*深度m为beginWord长度(默认beginWord和endWord长度一样)”，n为假设值，假设每次有效相邻是n个，姑且说是n的m次方吧，时间复杂度*

---
