# 栈和队列

## 简介

栈的特点是后入先出

![image.png](https://img.fuiboom.com/img/stack.png)

根据这个特点可以临时保存一些数据，之后用到依次再弹出来，常用于 DFS 深度搜索

队列一般常用于 BFS 广度搜索，类似一层一层的搜索

## Stack 栈

[min-stack](https://leetcode-cn.com/problems/min-stack/)

> 设计一个支持 push，pop，top 操作，并能在常数时间内检索到最小元素的栈。

思路：用两个栈实现，一个最小栈始终保证最小值在顶部

```c++
class MinStack {
    stack<int> x_stack;
    stack<int> min_stack;
public:
    MinStack() {
        min_stack.push(INT_MAX);
    }
    
    void push(int x) {
        x_stack.push(x);
        min_stack.push(min(min_stack.top(), x));
    }
    
    void pop() {
        x_stack.pop();
        min_stack.pop();
    }
    
    int top() {
        return x_stack.top();
    }
    
    int getMin() {
        return min_stack.top();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(val);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```

[evaluate-reverse-polish-notation](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

> **波兰表达式计算** > **输入:** `["2", "1", "+", "3", "*"]` > **输出:** 9
>
> **解释:** `((2 + 1) * 3) = 9`

思路：通过栈保存原来的元素，遇到表达式弹出运算，再推入结果，重复这个过程

```c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<int> s;
        unordered_set<string> oper{"+","-","*","/"};
        for(auto &c:tokens){
            // 数字就入栈
            if(oper.find(c)==oper.end())
                s.push(stoi(c));
            else{
                // 运算符就把前两位出栈做计算再入栈就是结果
                int v2=s.top();
                s.pop();
                int v1=s.top();
                s.pop();
                if(c=="+")
                    s.push(v1+v2);
                if(c=="-")
                    s.push(v1-v2);
                if(c=="*")
                    s.push(v1*v2);
                if(c=="/")
                    s.push(v1/v2);
            }
        }
        return s.top();
    }
};
```

[decode-string](https://leetcode-cn.com/problems/decode-string/)

> 给定一个经过编码的字符串，返回它解码后的字符串。
> s = "3[a]2[bc]", 返回 "aaabcbc".
> s = "3[a2[c]]", 返回 "accaccacc".
> s = "2[abc]3[cd]ef", 返回 "abcabccdcdcdef".

思路：通过栈辅助进行操作

```c++
class Solution {
public:

    // 返回当前位置的连续数字
    string getDigits(string &s, int &ptr)
    {
        string ret = "";
        while (isdigit(s[ptr])) ret += s[ptr++];
        return ret;
    }
    // vector to string
    string getString(vector <string> &v)
    {
        string ret = "";
        for (auto &s : v) ret += s;
        return ret;
    }

    string decodeString(string s) {
        vector<string> stk;
        int ptr = 0;

        while (ptr < s.size())
        {
            char cur = s[ptr];
            if (isdigit(cur))
            {
                // 获取连续的数字进栈
                string digits = getDigits(s, ptr);
                stk.push_back(digits);
            }
            // 获取一个字母进栈
            else if (isalpha(cur) || cur == '[')
            {
                stk.push_back(string(1, s[ptr++])); // 用string构造一下char
            }
            else{
                // 碰到 ']' 时候往前遍历到 '[' 前获取decode反转字符
                ptr ++;
                vector <string> sub;
                while (stk.back() != "[")
                {
                    sub.push_back(stk.back());
                    stk.pop_back();
                }
                reverse(sub.begin(), sub.end()); // 反转得到字符
                stk.pop_back();// 左括号出栈
                int repeatTime = stoi(stk.back());
                string t = getString(sub);
                string o = "";
                stk.pop_back(); // 重复次数出栈
                while (repeatTime -- ) o += t;
                stk.push_back(o); 
            }
        }

        return getString(stk);
    }
};
```

[binary-tree-inorder-traversal](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

> 给定一个二叉树，返回它的*中序*遍历。

```c++
// 思路：通过stack 保存已经访问的元素，用于原路返回
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> stk;
        while (root != nullptr || !stk.empty()) {
            while (root != nullptr) {
                stk.push(root);
                root = root->left;
            }
            root = stk.top();
            stk.pop();
            res.push_back(root->val);
            root = root->right;
        }
        return res;
    }
};
```

[number-of-islands](https://leetcode-cn.com/problems/number-of-islands/)

> 给定一个由  '1'（陆地）和 '0'（水）组成的的二维网格，计算岛屿的数量。一个岛被水包围，并且它是通过水平方向或垂直方向上相邻的陆地连接而成的。你可以假设网格的四个边均被水包围。

思路：通过深度搜索遍历可能性（注意标记已访问元素）

```c++
class Solution {
public:
    int dx[4] = {0, 0, 1, -1};
    int dy[4] = {-1, 1, 0, 0};

    void dfs(vector<vector<char>>& grid, int r, int c)
    {
        int nr = grid.size();
        int nc = grid[0].size();
        grid[r][c] = '0'; // 当前点置为0
        for (int k = 0; k < 4; k ++) // 遍历上下左右
        {
            int newr = r + dx[k], newc = c + dy[k];
            if (newr >= 0 && newr < nr && newc >= 0 && newc < nc && grid[newr][newc] == '1')
            {
                dfs(grid, newr, newc);
            }
        }
    }

    // 遍历得到1时把所有连接相邻的1都变成0
    int numIslands(vector<vector<char>>& grid) {
        int r = grid.size();
        if (r == 0) return 0;
        int c = grid[0].size();

        int res = 0;
        for (int i = 0; i < r; i ++)
        {
            for (int j = 0; j < c; j ++)
            {
                if (grid[i][j] == '1')
                {
                    res += 1;
                    dfs(grid, i, j);
                }
            }
        }
        return res;
    }
};
```

[largest-rectangle-in-histogram](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)

> 给定 _n_ 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。
> 求在该柱状图中，能够勾勒出来的矩形的最大面积。

思路：求以当前柱子为高度的面积，即转化为寻找小于当前值的左右两边值

![image.png](https://img.fuiboom.com/img/stack_rain.png)

用栈保存小于当前值的左的元素

![image.png](https://img.fuiboom.com/img/stack_rain2.png)

```c++
class Solution {
public:
    // 单调栈找所有两边第一个比枚举的高矮的位置得到宽
    int largestRectangleArea(vector<int>& heights) {

        int n = heights.size();
        // left, right存每个i位置高左右两边第一个比它矮的数的下标
        vector<int> left(n), right(n);

        stack<int> stk;
        for (int i = 0; i < n; i ++)
        {
            while (!stk.empty() && heights[stk.top()] >= heights[i]) stk.pop();
            left[i] = stk.empty() ? -1 : stk.top();
            stk.push(i);
        }

        stk = stack<int> ();
        for (int i = n - 1; i >= 0; i --)
        {
            while (!stk.empty() && heights[stk.top()] >= heights[i]) stk.pop();
            right[i] = stk.empty() ? n : stk.top();
            stk.push(i);
        }

        int res = 0;
        for (int i = 0; i < n; i ++) res = max(res, (right[i] - left[i] - 1) * heights[i]);

        return res;

    }
};
```

## Queue 队列

常用于 BFS 宽度优先搜索

[implement-queue-using-stacks](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

> 使用栈实现队列

```c++
class MyQueue {
private:
    stack<int> inStack, outStack;

    void in2out() {
        while (!inStack.empty()) {
            outStack.push(inStack.top());
            inStack.pop();
        }
    }

public:
    MyQueue() {}

    void push(int x) {
        inStack.push(x);
    }

    int pop() {
        if (outStack.empty()) {
            in2out();
        }
        int x = outStack.top();
        outStack.pop();
        return x;
    }

    int peek() {
        if (outStack.empty()) {
            in2out();
        }
        return outStack.top();
    }

    bool empty() {
        return inStack.empty() && outStack.empty();
    }
};
/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
```

[binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

> 返回二叉树层次遍历

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (root == nullptr) return res;

        queue<TreeNode*> q;
        q.push(root);

        while (!q.empty())
        {
            int curr_size = q.size();
            res.push_back(vector<int> ());
            for (int i = 0; i < curr_size; i ++)
            {
                auto node = q.front();
                q.pop();
                res.back().push_back(node->val);
                if (node->left) q.push(node->left);
                if (node->right) q.push(node->right);
            }
        }

        return res;
    }
};
```

[01-matrix](https://leetcode-cn.com/problems/01-matrix/)

> 给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。
> 两个相邻元素间的距离为 1

```c++
// BFS 从0进队列，弹出之后计算上下左右的结果，将上下左右重新进队列进行二层操作
// 0 0 0 0
// 0 x 0 0
// x x x 0
// 0 x 0 0

// 0 0 0 0
// 0 1 0 0
// 1 x 1 0
// 0 1 0 0

// 0 0 0 0
// 0 1 0 0
// 1 2 1 0
// 0 1 0 0
class Solution {
public:
    // 广度优先搜索
    vector<vector<int>> updateMatrix(vector<vector<int>>& matrix) {
        int numr = matrix.size();
        int numc = matrix[0].size();
        vector<pair<int,int>> around = {{0,1},{0,-1},{-1,0},{1,0}}; // 上下左右
        vector<vector<int>> result(numr, vector<int>(numc, INT_MAX));
        queue<pair<int,int>> que;
        for(int i = 0; i < numr; i++){
            for(int j = 0; j < numc; j++){
                if(matrix[i][j] == 0){
                    result[i][j] = 0; // 所有0的点最近距离就是0
                    que.push({i, j});
                }
            }
        }
        while(!que.empty()){
            auto temp = que.front();
            que.pop();
            for(int i = 0; i < 4; i++){
                int x = temp.first + around[i].first;
                int y = temp.second + around[i].second;
                if(x >= 0 && x < numr && y >= 0 && y < numc){
                    if(result[x][y] > result[temp.first][temp.second] + 1){
                        result[x][y] = result[temp.first][temp.second] + 1;
                        que.push({x, y}); // 入队
                    }
                }

            }
        }
        return result;
    }
};
```

## 总结

- 熟悉栈的使用场景
  - 后入先出，保存临时值
  - 利用栈 DFS 深度搜索
- 熟悉队列的使用场景
  - 利用队列 BFS 广度搜索

## 练习

- [ ] [min-stack](https://leetcode-cn.com/problems/min-stack/)
- [ ] [evaluate-reverse-polish-notation](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)
- [ ] [decode-string](https://leetcode-cn.com/problems/decode-string/)
- [ ] [binary-tree-inorder-traversal](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)
- [ ] [number-of-islands](https://leetcode-cn.com/problems/number-of-islands/)
- [ ] [largest-rectangle-in-histogram](https://leetcode-cn.com/problems/largest-rectangle-in-histogram/)
- [ ] [implement-queue-using-stacks](https://leetcode-cn.com/problems/implement-queue-using-stacks/)
- [ ] [binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
- [ ] [01-matrix](https://leetcode-cn.com/problems/01-matrix/)
