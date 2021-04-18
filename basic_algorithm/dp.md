# 动态规划

## 背景

先从一道题目开始~

如题  [triangle](https://leetcode-cn.com/problems/triangle/)

> 给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

例如，给定三角形：

```text
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```

自顶向下的最小路径和为  11（即，2 + 3 + 5 + 1 = 11）。

使用 DFS（遍历 或者 分治法）

遍历

![image.png](https://img.fuiboom.com/img/dp_triangle.png)

分治法

![image.png](https://img.fuiboom.com/img/dp_dc.png)

优化 DFS，缓存已经被计算的值（称为：记忆化搜索 本质上：动态规划）

![image.png](https://img.fuiboom.com/img/dp_memory_search.png)

动态规划就是把大问题变成小问题，并解决了小问题重复计算的方法称为动态规划

动态规划和 DFS 区别

- 二叉树 子问题是没有交集，所以大部分二叉树都用递归或者分治法，即 DFS，就可以解决
- 像 triangle 这种是有重复走的情况，**子问题是有交集**，所以可以用动态规划来解决

动态规划，自底向上

```c++
int minimumTotal(vector<vector<int>>& triangle) {
    if(triangle.size() == 0 || triangle[0].size() == 0) return 0;

    // 1、状态定义：f[i][j] 表示从i,j出发，到达最后一层的最短路径
    int l = triangle.size();
    // 2、初始化
    vector<vector<int>> f;
    for(int i = 0; i < l; i++){
        vector<int> g;
        for(int j = 0; j < triangle[i].size(); j++){
             g.push_back(triangle[i][j]);
        }
        f.push_back(g);
    }

    // 3、递推求解
    for(int i = triangle.size() - 2; i >= 0; i--){
        for(int j = 0; j < triangle[i].size(); j++){
            f[i][j] = min(f[i + 1][j], f[i + 1][j + 1]) + triangle[i][j];
        }
    }

    // 4、答案
    return f[0][0];
}
```

动态规划，自顶向下

```c++
// 测试用例：
// [
// [2],
// [3,4],
// [6,5,7],
// [4,1,8,3]
// ]
int minimumTotal(vector<vector<int>>& triangle) {
    if(triangle.size() == 0 || triangle[0].size() == 0) return 0;

    // 1、状态定义：f[i][j] 表示从0,0出发，到达i,j的最短路径
    int l = triangle.size();
    // 2、初始化
    vector<vector<int>> f;
    for(int i = 0; i < l; i++){
        vector<int> g;
        for(int j = 0; j < triangle[i].size(); j++){
             g.push_back(triangle[i][j]);
        }
        f.push_back(g);
    }

    // 3、递推求解
    for(int i = 1; i < l; i++){
        for(int j = 0; j < triangle[i].size(); j++){
            // 这里分为两种情况：
            // 1、上一层没有左边值
            // 2、上一层没有右边值
            if (j-1 < 0) {
                f[i][j] = f[i-1][j] + triangle[i][j];
            } else if (j >= f[i-1].size()) {
                f[i][j] = f[i-1][j-1] + triangle[i][j];
            } else {
                f[i][j] = min(f[i-1][j], f[i-1][j-1]) + triangle[i][j];
            }
        }
    }

    // 4、答案
    int result = f[l-1][0];
    for(int i = 1; i < f[l-1].size(); i++){
        result = min(f[l - 1][i], result);
    }

    return result;
}
```

优化空间负责度
```c++
int minimumTotal(vector<vector<int>>& triangle) {
     int n = triangle.size();
     vector<int> dp(n + 1);
     for(int i = n - 1; i >= 0; i--){
         for(int j = 0; j <= i; j++){
             dp[j] = min(dp[j], dp[j + 1]) + triangle[i][j];
         }
     }

     return dp[0];
}
```

## 递归和动规关系

递归是一种程序的实现方式：函数的自我调用

```go
Function(x) {
	...
	Funciton(x-1);
	...
}
```

动态规划：是一种解决问 题的思想，大规模问题的结果，是由小规模问 题的结果运算得来的。动态规划可用递归来实现(Memorization Search)

## 使用场景

满足两个条件

- 满足以下条件之一
  - 求最大/最小值（Maximum/Minimum ）
  - 求是否可行（Yes/No ）
  - 求可行个数（Count(\*) ）
- 满足不能排序或者交换（Can not sort / swap ）

如题：[longest-consecutive-sequence](https://leetcode-cn.com/problems/longest-consecutive-sequence/)  位置可以交换，所以不用动态规划

## 四点要素

1. **状态 State**
   - 灵感，创造力，存储小规模问题的结果
2. 方程 Function
   - 状态之间的联系，怎么通过小的状态，来算大的状态
3. 初始化 Intialization
   - 最极限的小状态是什么, 起点
4. 答案 Answer
   - 最大的那个状态是什么，终点

## 常见四种类型

1. Matrix DP (10%)
1. Sequence (40%)
1. Two Sequences DP (40%)
1. Backpack (10%)

> 注意点
>
> - 贪心算法大多题目靠背答案，所以如果能用动态规划就尽量用动规，不用贪心算法

## 1、矩阵类型（10%）

### [minimum-path-sum](https://leetcode-cn.com/problems/minimum-path-sum/)

> 给定一个包含非负整数的  *m* x *n*  网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

思路：动态规划
1、state: f[x][y]从起点走到 x,y 的最短路径
2、function: f[x][y] = min(f[x-1][y], f[x][y-1]) + A[x][y]
3、intialize: f[0][0] = A[0][0]、f[i][0] = sum(0,0 -> i,0)、 f[0][i] = sum(0,0 -> 0,i)
4、answer: f[n-1][m-1]

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.size() == 0 || grid[0].size() == 0) {
            return 0;
        }
        int rows = grid.size(), columns = grid[0].size();
        // dp[i][j] 表示从起点走到 (i, j)的最小距离
        vector<vector<int>> dp(rows, vector<int>(columns));
        dp[0][0] = grid[0][0]; // 初始化
        // 第0列的路径和就是前缀和
        for (int i = 1; i < rows; i++) {
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }
        // 第0行的的路径和就是前缀和
        for (int j = 1; j < columns; j++) {
            dp[0][j] = dp[0][j - 1] + grid[0][j];
        }
        for (int i = 1; i < rows; i++) {
            for (int j = 1; j < columns; j++) {
                // 不在第0行第0列最小路径可以是左边或者上面来的路径加上当前的权重
                dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }
        return dp[rows - 1][columns - 1];
    }
};
```

### [unique-paths](https://leetcode-cn.com/problems/unique-paths/)

> 一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。
> 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。
> 问总共有多少条不同的路径？

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        // dp[i][j] 表示从起点移动到 (i, j) 总共有多少条不同路径
        vector<vector<int>> dp(m, vector<int>(n));
        // 初始化，由于只能向下或者向右, 因此移动到第0行第0列上的路径都只有一种
        for (int i = 0; i < m; i ++) dp[i][0] = 1;
        for (int j = 0; j < n; j ++) dp[0][j] = 1;

        for (int i = 1; i < m; i ++)
            for (int j = 1; j < n; j ++)
                // (i, j)的不同路径就是左边来的路径+上面来的路径
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1];

        return dp[m-1][n-1];
    }
};
```

### [unique-paths-ii](https://leetcode-cn.com/problems/unique-paths-ii/)

> 一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。
> 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。
> 问总共有多少条不同的路径？
> 现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

```c++
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        if (obstacleGrid[0][0] == 1) return 0; //开头就是障碍物
        int m = obstacleGrid.size(), n = obstacleGrid[0].size();
        // dp[i][j] 表示从起点到(i, j)总共有多少条不同路径
        vector<vector<int>> dp(m, vector<int>(n, 1));
        // 第0列先初始化路径为1种, 碰到障碍物的话后面的路径就无法到达了就是0
        for (int i = 1; i < m; i ++)
        {
            if (obstacleGrid[i][0] == 1 || dp[i - 1][0] == 0) dp[i][0] = 0;
        }
        // 第0行先初始化路径为1种, 碰到障碍物的话后面的路径就无法到达了就是0
        for (int j = 1; j < n; j ++)
        {
            if (obstacleGrid[0][j] == 1 || dp[0][j - 1] == 0) dp[0][j] = 0;
        }

        for (int i = 1; i < m; i ++)
        {
            for (int j = 1; j < n; j ++)
            {
                // (i, j) 位置有障碍物的话则没有路径可以到达这个地方
                if (obstacleGrid[i][j] == 1) dp[i][j] = 0;
                // 从左边来的路径+上面来的路径
                else dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
            }
        }

        return dp[m - 1][n - 1];
    }
};
```

## 2、序列类型（40%）

### [climbing-stairs](https://leetcode-cn.com/problems/climbing-stairs/)

> 假设你正在爬楼梯。需要  *n*  阶你才能到达楼顶。

```c++
class Solution {
public:
    int climbStairs(int n) {
        if(n < 3) return n;
        // 前两个状态， 前一个状态
        int a = 1, b = 2;
        for(int i = 3; i <= n; i++){
            int sum = a + b; // 当前状态 = 前两个状态+前一个状态
            // 更新状态
            a = b; // 下个迭代的前两个状态更新为前一个状态
            b = sum; // 下个迭代的前一个状态更新为计算完的当前状态
        }

        return b;
    }
};
```

### [jump-game](https://leetcode-cn.com/problems/jump-game/)

> 给定一个非负整数数组，你最初位于数组的第一个位置。
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
> 判断你是否能够到达最后一个位置。

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        int rightmost = 0;
        for (int i = 0; i < n; i ++) {
            if (i <= rightmost) {
                // 找到当前能跳的最远的地方
                rightmost = max(rightmost, i + nums[i]);
                if (rightmost >= n - 1) {
                    return true;
                }
            }
        }
        return false;
    }
};
```

### [jump-game-ii](https://leetcode-cn.com/problems/jump-game-ii/)

> 给定一个非负整数数组，你最初位于数组的第一个位置。
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
> 你的目标是使用最少的跳跃次数到达数组的最后一个位置。

```c++
class Solution {
public:
int jump(vector<int>& nums)
    {
        int ans = 0;
        int end = 0;
        int maxPos = 0;
        for (int i = 0; i < nums.size() - 1; i++)
        {
            maxPos = max(nums[i] + i, maxPos); // 当前位置能跳到的最远位置
            if (i == end)
            {
                end = maxPos;
                ans++;
            }
        }
        return ans;
    }
};
```

### [palindrome-partitioning-ii](https://leetcode-cn.com/problems/palindrome-partitioning-ii/)

> 给定一个字符串 _s_，将 _s_ 分割成一些子串，使每个子串都是回文串。
> 返回符合要求的最少分割次数。

```c++
class Solution {
public:
    // 预处理g[i][j]表示字符串(i, j)是否为回文串
    // f[i] 表示字符串(0, i)的最少分隔次数
    // f[i]的状态为g[0~i][i]为回文串的状态cut一次得来
    int minCut(string s) {

        int n = s.size();

        // 预处理是否为回文串
        vector<vector<int>> g(n, vector<int>(n, false));
        for (int len = 0; len < n; len ++)
        {
            for (int i = 0; i + len < n; i ++)
            {
                int j = i + len; // 右端点
                if (len == 0) g[i][j] = true;
                else if (len == 1 && s[i] == s[j]) g[i][j] = true;
                else g[i][j] = (s[i] == s[j] && g[i + 1][j - 1]);
            }
        }
        // f表示最少分隔次数
        vector<int> f(n, 2e9); // 找最小值初始化为很大的数
        f[0] = 0; // 自身就是回文串不用分隔
        for (int i = 1; i < n; i ++)
        {
            if (g[0][i]) f[i] = 0; // 开头到i都是回文串不用分隔
            else{
                for (int j = 0; j < i; j ++) // 不是回文串的话进行状态转移
                {
                    if (g[j + 1][i]) f[i] = min(f[i], f[j] + 1); // 分隔一次
                }
            }
        }

        return f[n - 1];
    }
};
```

注意点

- 判断回文字符串时，可以提前用动态规划算好，减少时间复杂度

### [longest-increasing-subsequence](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

> 给定一个无序的整数数组，找到其中最长上升子序列的长度。

```c++
class Solution {
public:
    // dp[j] 代表 nums[0…j] 中以 nums[j] 结尾的最长上升子序列
    int lengthOfLIS(vector<int>& nums) {
        if (nums.empty()) return 0;

        vector<int> dp(nums.size(), 0);
        for (int i = 0; i < nums.size(); i ++)
        {
            dp[i] = 1; // 坑， 初始状态, 自身最长子序列是1
            for (int j = 0; j < i; j ++)
            {
                if (nums[j] < nums[i]) dp[i] = max(dp[i], dp[j] + 1);
            }
        }

        return *max_element(dp.begin(), dp.end());
    }
};
```

### [word-break](https://leetcode-cn.com/problems/word-break/)

> 给定一个**非空**字符串  *s*  和一个包含**非空**单词列表的字典  *wordDict*，判定  *s*  是否可以被空格拆分为一个或多个在字典中出现的单词。

```c++
class Solution {
public:
    // 用一个集合存储字典里的单词
    // dp[i] 表示(0~i)的字符串是否可以被字典里的单词组合出来
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> wordDictSet;
        for (auto word : wordDict) wordDictSet.insert(word);

        vector<bool> dp(s.size() + 1);
        dp[0] = true; // 表示空串且合法

        for (int i = 1; i <= s.size(); i ++)
        {
            for (int j = 0; j < i; j ++)
            {
                if (dp[j] && wordDictSet.find(s.substr(j, i - j)) != wordDictSet.end())
                {
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[s.size()];
    }
};
```

小结

常见处理方式是给 0 位置占位，这样处理问题时一视同仁，初始化则在原来基础上 length+1，返回结果 f[n]

- 状态可以为前 i 个
- 初始化 length+1
- 取值 index=i-1
- 返回值：f[n]或者 f[m][n]

## Two Sequences DP（40%）

### [longest-common-subsequence](https://leetcode-cn.com/problems/longest-common-subsequence/)

> 给定两个字符串  text1 和  text2，返回这两个字符串的最长公共子序列。
> 一个字符串的   子序列   是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
> 例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。两个字符串的「公共子序列」是这两个字符串所共同拥有的子序列。

```c++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        // dp[i][j] a前i个和b前j个字符最长公共子序列
        // dp[m+1][n+1]
        //   ' a d c e
        // ' 0 0 0 0 0
        // a 0 1 1 1 1
        // c 0 1 1 2 1
        //
        int m = text1.size(), n = text2.size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1));

        for(int i = 1; i <= m; i++){
            for(int j = 1; j <= n; j++){
                if(text1[i - 1] == text2[j - 1]){
                    // 更新子序列，前i-1,j-1个公共子序列相同
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                }else{
                    // 否则取各自最大的状态
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }

        return dp[m][n];
    }
};
```

注意点

- c++ 切片初始化

```c++
vector<vector<int>> A1(m, vector<int>(n));
vector<vector<int>> A2(m + 1, vector<int>(n + 1));
for (int i = 0; i < m; i ++)
{
    for (int j = 0; j < n; j ++)
    {
        A2[i + 1][j + 1] = A1[i][j];
    }
}
// copy
vector<vector<int>> A3(A1);
```

- 从 1 开始遍历到最大长度
- 索引需要减一

### [edit-distance](https://leetcode-cn.com/problems/edit-distance/)

> 给你两个单词  word1 和  word2，请你计算出将  word1  转换成  word2 所使用的最少操作数  
> 你可以对一个单词进行如下三种操作：
> 插入一个字符
> 删除一个字符
> 替换一个字符

思路：和上题很类似，相等则不需要操作，否则取删除、插入、替换最小操作次数的值+1

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        // dp[i][j] 表示a字符串的前i个字符编辑为b字符串的前j个字符最少需要多少次操作
        // dp[i][j] = OR(dp[i-1][j-1]，a[i]==b[j],min(dp[i-1][j],dp[i][j-1],dp[i-1][j-1])+1)
        int m = word1.size(), n = word2.size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1));
        // 其中一个子串为空的话需要另一个子串长度的操作次数
        for(int i = 0; i <= m; i++){
            dp[i][0] = i;
        }
        for(int i = 0; i <=n; i++){
            dp[0][i] = i;
        }

        for(int i = 1; i <= m; i++){
            for(int j = 1; j <= n; j++){
                if(word1[i - 1] == word2[j - 1]){
                    // 相等则不需要操作
                    dp[i][j] = dp[i - 1][j - 1]; 
                }else{
                    // 否则取删除、插入、替换最小操作次数的值+1
                    dp[i][j] = min(min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                }
            }
        }

        return dp[m][n];
    }
};
```

说明

> 另外一种做法：MAXLEN(a,b)-LCS(a,b)

## 零钱和背包（10%）

### [coin-change](https://leetcode-cn.com/problems/coin-change/)

> 给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回  -1。

思路：和其他 DP 不太一样，i 表示钱或者容量

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        // 状态 dp[i]表示金额为i时，组成的最小硬币个数
        // 推导 dp[i]  = min(dp[i-1], dp[i-2], dp[i-5])+1, 前提 i-coins[j] > 0
        // 初始化为最大值 dp[i]=amount+1
        // 返回值 dp[n] or dp[n]>amount =>-1
        vector<int> dp(amount + 1, amount + 1);
        dp[0] = 0;
        for(int i = 1; i <= amount; i++){
            for(int j = 0; j < coins.size(); j++){
                if(i - coins[j] >= 0){
                    dp[i] = min(dp[i], dp[i - coins[j]] + 1);
                }
            }
        }

        if(dp[amount] > amount){
            return -1;
        }

        return dp[amount];
    }
};
```

注意

> dp[i-a[j]] 决策 a[j]是否参与

### 01背包

> 有 N 件物品和一个容量是 V 的背包。每件物品只能使用一次。第 i 件物品的体积是 vi，价值是 wi。求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。
输出最大价值。
```c++
#include <iostream>
using namespace std;

const int N = 1010;

int v[N], w[N];
int n, m;
int f[N][N];

int main()
{
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++) cin >> v[i] >> w[i];
    
    for (int i = 1; i <= n; i ++)
        for (int j = 1; j <= m; j ++)
            {
                // 不选当前物品
                f[i][j] = f[i - 1][j];
                // 选择当前物品
                if (v[i] <= j) f[i][j] = max(f[i][j], f[i - 1][j - v[i]] + w[i]);
            }
            
    cout << f[n][m] << endl;
    
    return 0;
}
```

### 完全背包
> 有 N 种物品和一个容量是 V 的背包，每种物品都有无限件可用。第 i 种物品的体积是 vi，价值是 wi。求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。
输出最大价值。
```c++
#include <iostream>
using namespace std;

const int N = 1010;

int v[N], w[N];
int f[N][N];

int n, m;

int main()
{
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++) cin >> v[i] >> w[i];
    
    for (int i = 1; i <= n; i ++)
        for (int j = 1; j <= m; j ++)
            // 当背包不满时候可以一直装当前物品
            for(int k = 0; v[i] * k <= j; k ++)
                {
                    f[i][j] = max(f[i][j], f[i - 1][j - v[i] * k] + w[i] * k);
                }
                
    cout << f[n][m] << endl;
    
    return 0;
}
```

### 多重背包
> 有 N 种物品和一个容量是 V 的背包。第 i 种物品最多有 si 件，每件体积是 vi，价值是 wi。求解将哪些物品装入背包，可使物品体积总和不超过背包容量，且价值总和最大。
输出最大价值。
```c++
#include <iostream>

using namespace std;

const int N = 2010;

int v[N], w[N], s[N];

int f[N][N];

int n, m;

int main()
{
    cin >> n >> m;
    
    for (int i = 1; i <= n; i ++) cin >> v[i] >> w[i] >> s[i];
    
    for (int i = 1; i <= n; i ++)
        for (int j = 1; j <= m; j ++)
            // 在达到物品数量前且总体积不超过背包的时候
            for (int k = 0; k <= s[i] && k * v[i] <= j; k ++)
                f[i][j] = max(f[i][j], f[i - 1][j - v[i] * k] + w[i] * k);
                
    cout << f[n][m] << endl;
    return 0;
}
```

> 背包问题一般都可以把物品价值以及背包容量都放到n+1的数组里方便求解

## 练习

Matrix DP (10%)

- [ ] [triangle](https://leetcode-cn.com/problems/triangle/)
- [ ] [minimum-path-sum](https://leetcode-cn.com/problems/minimum-path-sum/)
- [ ] [unique-paths](https://leetcode-cn.com/problems/unique-paths/)
- [ ] [unique-paths-ii](https://leetcode-cn.com/problems/unique-paths-ii/)

Sequence (40%)

- [ ] [climbing-stairs](https://leetcode-cn.com/problems/climbing-stairs/)
- [ ] [jump-game](https://leetcode-cn.com/problems/jump-game/)
- [ ] [jump-game-ii](https://leetcode-cn.com/problems/jump-game-ii/)
- [ ] [palindrome-partitioning-ii](https://leetcode-cn.com/problems/palindrome-partitioning-ii/)
- [ ] [longest-increasing-subsequence](https://leetcode-cn.com/problems/longest-increasing-subsequence/)
- [ ] [word-break](https://leetcode-cn.com/problems/word-break/)

Two Sequences DP (40%)

- [ ] [longest-common-subsequence](https://leetcode-cn.com/problems/longest-common-subsequence/)
- [ ] [edit-distance](https://leetcode-cn.com/problems/edit-distance/)

Backpack & Coin Change (10%)

- [ ] [coin-change](https://leetcode-cn.com/problems/coin-change/)