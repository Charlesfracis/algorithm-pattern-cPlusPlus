# 回溯法

## 背景

回溯法（backtrack）常用于遍历列表所有子集，是 DFS 深度搜索一种，一般用于全排列，穷尽所有可能，遍历的过程实际上是一个决策树的遍历过程。时间复杂度一般 O(N!)，它不像动态规划存在重叠子问题可以优化，回溯算法就是纯暴力穷举，复杂度一般都很高。

## 模板

```python
result = []
func backtrack(选择列表,路径):
    if 满足结束条件:
        result.add(路径)
        return
    for 选择 in 选择列表:
        做选择
        backtrack(选择列表,路径)
        撤销选择
```

核心就是从选择列表里做一个选择，然后一直递归往下搜索答案，如果遇到路径不通，就返回来撤销这次选择。

## 示例

### [subsets](https://leetcode-cn.com/problems/subsets/)

> 给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

遍历过程

![image.png](https://img.fuiboom.com/img/backtrack.png)

```c++
class Solution {
public:
    vector<vector<int>> res;
    void dfs(vector<int>& nums, vector<int>& combination, int index)
    {
        res.push_back(combination);

        for (int i = index; i < nums.size(); i ++) // 坑 i从index开始
        {
            combination.push_back(nums[i]);
            dfs(nums, combination, i + 1); // 下一层dfs从i+1开始
            combination.pop_back();
        }
    }

    vector<vector<int>> subsets(vector<int>& nums) {
        if (nums.empty()) return res;
        vector<int> combination;
        dfs(nums, combination, 0);
        return res;
    }
};
```

### [subsets-ii](https://leetcode-cn.com/problems/subsets-ii/)

> 给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。说明：解集不能包含重复的子集。

```c++
class Solution {
public:
    // 先排序，在dfs时候剪枝与上一层结果相同的数
    vector<vector<int>> res;

    void dfs(vector<int>& nums, vector<int>& combination, int index)
    {
        res.push_back(combination);

        for (int i = index; i < nums.size(); i ++)
        {
            // 剪枝
            if (i != index && nums[i] == nums[i - 1]) continue;
            combination.push_back(nums[i]);
            dfs(nums, combination, i + 1);
            combination.pop_back();
        }
    }
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<int> combination;
        sort(nums.begin(), nums.end()); // 记得排序
        dfs(nums, combination, 0);
        return res;
    }
};
```

### [permutations](https://leetcode-cn.com/problems/permutations/)

> 给定一个   没有重复   数字的序列，返回其所有可能的全排列。

思路：需要记录已经选择过的元素，满足条件的结果才进行返回

```c++
class Solution {
public:

    vector<vector<int>> res;
    vector<int> combine;

    // 暴力枚举，用一个used数组来跳过已经选择过的数
    void dfs(vector<int>& nums, vector<bool>& used)
    {
        if (combine.size() == nums.size())
        {
            res.push_back(combine);
            return;
        }

        for (int i = 0; i < nums.size(); i ++)
        {
            if (used[i]) continue;
            used[i] = true;
            combine.push_back(nums[i]);
            dfs(nums, used);
            combine.pop_back();
            used[i] = false;
        }
    }

    vector<vector<int>> permute(vector<int>& nums) {
        vector<bool> used(nums.size(), false);
        dfs(nums, used);
        return res;
    }
};
```

### [permutations-ii](https://leetcode-cn.com/problems/permutations-ii/)

> 给定一个可包含重复数字的序列，返回所有不重复的全排列。

```c++
class Solution {
public:
    // 先排序，dfs时候碰到上一层相同数值(且上一个数没有被访问过)的时候就跳过
    vector<vector<int>> res;

    void dfs(vector<int>& nums, vector<int>& combination, vector<bool>& used)
    {
        if (combination.size() == nums.size())
        {
            res.push_back(combination);
            return; // 坑，记得return
        }

        for (int i = 0; i < nums.size(); i ++)
        {
            if (used[i]) continue;
            // 碰到上一层相同的数且已经访问过了
            if (i != 0 && nums[i] == nums[i - 1] && used[i - 1]) continue;
            used[i] = true;
            combination.push_back(nums[i]);
            dfs(nums, combination, used);
            used[i] = false;
            combination.pop_back();
        }
    }

    vector<vector<int>> permuteUnique(vector<int>& nums) {
        if (nums.empty()) return res;

        vector<int> combination;
        vector<bool> used(nums.size(), false);

        sort(nums.begin(), nums.end()); // 记得排序

        dfs(nums, combination, used);
        return res;
    }
};
```

### [combination-sum](https://leetcode-cn.com/problems/combination-sum/)

> 给定一个无重复元素的数组 candidates(可以无限重复选) 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

```c++
class Solution {
public:
    void dfs(vector<int>& candidates, int target, vector<vector<int>>& ans, vector<int>& combine, int idx)
    {
        if (idx == candidates.size()) return;

        if (target == 0)
        {
            ans.push_back(combine);
            return;
        }

        // 直接跳过
        dfs(candidates, target, ans, combine, idx + 1);
        // 取当前数
        if (target - candidates[idx] >= 0)
        {
            combine.push_back(candidates[idx]);
            // 因为可以重复选择所以idx
            dfs(candidates, target - candidates[idx], ans, combine, idx); 
            combine.pop_back();
        }
    }

    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int> combine;
        dfs(candidates, target, ans, combine, 0);
        return ans;
    }
};
```

### [combination-sum-ii](https://leetcode-cn.com/problems/combination-sum-ii/)

> 给定一个数组 candidates(每个数只能选一次) 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

```c++
class Solution {
public:
    vector<pair<int, int>> freq; // 记录数值以及出现频率
    vector<vector<int>> res; // 结果

    // index: 当前递归到哪个数, rest: 剩余0表示找到组合
    void dfs(vector<int>& combination, int index, int rest)
    {
        // 坑， 先保存答案
        if (rest == 0) // 找到一组答案
        {
            res.push_back(combination);
            return;
        }

        // 选完或者剩余数值大于rest了
        if (index == freq.size() || rest < freq[index].first) return;

        // 不选当前数
        dfs(combination, index + 1, rest);

        // 选当前数， 不包含重复解
        int num = freq[index].first;
        int most = min(rest / num, freq[index].second); // 当前数最多能选次数
        for (int i = 1; i <= most; i ++) // 确定选择了，所以从1开始
        {
            combination.push_back(num);
            dfs(combination, index + 1, rest - i * num); // 坑
        }
        // 回溯在最后
        for (int i = 1; i <= most; i ++) combination.pop_back();
    }

    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        sort(candidates.begin(), candidates.end()); // 排序
        // 从小到大构造 数值以及出现的频率
        for (auto num : candidates)
        {
            if (freq.empty() || freq.back().first != num) freq.push_back({num, 1});
            else freq.back().second += 1;
        }
        vector<int> combination;

        dfs(combination, 0, target);

        return res;
    }
};
```

### [letter-combinations-of-a-phone-number](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

> 给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。答案可以按 任意顺序 返回。

```c++
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        vector<string> combinations;
        if (digits.empty()) {
            return combinations;
        }
        unordered_map<char, string> phoneMap{
            {'2', "abc"},
            {'3', "def"},
            {'4', "ghi"},
            {'5', "jkl"},
            {'6', "mno"},
            {'7', "pqrs"},
            {'8', "tuv"},
            {'9', "wxyz"}
        };
        string combination;
        backtrack(combinations, phoneMap, digits, 0, combination);
        return combinations;
    }

    // DFS
    void backtrack(vector<string>& combinations, const unordered_map<char, string>& phoneMap, const string& digits, int index, string& combination) {
        if (index == digits.length()) {
            combinations.push_back(combination);
            return;
        } else {
            char digit = digits[index];
            const string& letters = phoneMap.at(digit);
            for (const char& letter: letters) {
                combination += letter;
                backtrack(combinations, phoneMap, digits, index + 1, combination);
                combination.pop_back(); // 还原现场
            }
        }
    }
};
```

### [palindrome-partitioning](https://leetcode-cn.com/problems/palindrome-partitioning/)

> 给你一个字符串 s，请你将 s 分割成一些子串，使每个子串都是 回文串 。返回 s 所有可能的分割方案。

```c++
class Solution {
public:
    // 动态规划预处理回文串判断， dfs获取分隔方案
    vector<vector<string>> res;
    vector<vector<int>> f;
    vector<string> combination;
    int n;

    void dfs(const string& s, int i) {
        if (i == n) {
            res.push_back(combination);
            return;
        }
        for (int j = i; j < n; ++j) {
            if (f[i][j]) {
                combination.push_back(s.substr(i, j - i + 1)); // j - i + 1
                dfs(s, j + 1);
                combination.pop_back();
            }
        }
    }

    vector<vector<string>> partition(string s) {
        n = s.size();
        f.assign(n, vector<int>(n, 0));
        // 找出所有回文串位置
        for (int len = 0; len < n; len ++) // 枚举所有字符长度
        {
            for (int i = 0; i + len < n; i ++) // 当前字左端点
            {
                int j = i + len; // 右端点
                if (len == 0) f[i][j] = 1; // 单个字符为回文串
                else if (len == 1) f[i][j] = (s[i] == s[j]); // 两个字符相同为回文串
                // 左右两边字符相同，且往里缩一位也是回文串的情况下是回文串
                else f[i][j] = (s[i] == s[j] && f[i + 1][j - 1]); 
            }
        }

        dfs(s, 0);
        return res;

    }
};
```

### [restore-ip-addresses](https://leetcode-cn.com/problems/restore-ip-addresses/)

> 给定一个只包含数字的字符串，用以表示一个 IP 地址，返回所有可能从 s 获得的 有效 IP 地址 。你可以按任何顺序返回答案。

>有效 IP 地址 正好由四个整数（每个整数位于 0 到 255 之间组成，且不能含有前导 0），整数之间用 '.' 分隔。

```c++
class Solution {
private:
    static constexpr int SEG_COUNT = 4;

private:
    vector<string> ans;
    vector<int> segments;

public:
    void dfs(const string& s, int segId, int segStart) {
        // 如果找到了 4 段 IP 地址并且遍历完了字符串，那么就是一种答案
        if (segId == SEG_COUNT) {
            if (segStart == s.size()) {
                string ipAddr;
                for (int i = 0; i < SEG_COUNT; ++i) {
                    ipAddr += to_string(segments[i]);
                    if (i != SEG_COUNT - 1) {
                        ipAddr += ".";
                    }
                }
                ans.push_back(move(ipAddr));
            }
            return;
        }

        // 如果还没有找到 4 段 IP 地址就已经遍历完了字符串，那么提前回溯
        if (segStart == s.size()) {
            return;
        }

        // 由于不能有前导零，如果当前数字为 0，那么这一段 IP 地址只能为 0
        if (s[segStart] == '0') {
            segments[segId] = 0;
            dfs(s, segId + 1, segStart + 1);
        }

        // 一般情况，枚举每一种可能性并递归
        int addr = 0;
        for (int segEnd = segStart; segEnd < s.size(); ++segEnd) {
            addr = addr * 10 + (s[segEnd] - '0');
            if (addr > 0 && addr <= 0xFF) {
                segments[segId] = addr;
                dfs(s, segId + 1, segEnd + 1);
            } else {
                break;
            }
        }
    }

    vector<string> restoreIpAddresses(string s) {
        segments.resize(SEG_COUNT);
        dfs(s, 0, 0);
        return ans;
    }
};
```

## 练习

- [ ] [subsets](https://leetcode-cn.com/problems/subsets/)
- [ ] [subsets-ii](https://leetcode-cn.com/problems/subsets-ii/)
- [ ] [permutations](https://leetcode-cn.com/problems/permutations/)
- [ ] [permutations-ii](https://leetcode-cn.com/problems/permutations-ii/)

挑战题目

- [ ] [combination-sum](https://leetcode-cn.com/problems/combination-sum/)
- [ ] [combination-sum-ii](https://leetcode-cn.com/problems/combination-sum-ii/)
- [ ] [letter-combinations-of-a-phone-number](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)
- [ ] [palindrome-partitioning](https://leetcode-cn.com/problems/palindrome-partitioning/)
- [ ] [restore-ip-addresses](https://leetcode-cn.com/problems/restore-ip-addresses/)
