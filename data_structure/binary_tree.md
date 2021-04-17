# 二叉树

## 知识点

### 二叉树遍历

**前序遍历**：**先访问根节点**，再前序遍历左子树，再前序遍历右子树

**中序遍历**：先中序遍历左子树，**再访问根节点**，再中序遍历右子树

**后序遍历**：先后序遍历左子树，再后序遍历右子树，**再访问根节点**


注意点

- 以根访问顺序决定是什么遍历
- 左子树都是优先右子树

#### 前序递归

```c++
void preorderHelper(TreeNode *node){
    if(node == nullptr) return;
    result.push_back(node->val);
    preorderHelper(node->left);
    preorderHelper(node->right);
}
```

#### 前序非递归

```c++
//通过非递归遍历
vector<int> preorderTraversal(TreeNode* root) {
    vector<int> res;  //保存结果
    stack<TreeNode*> call;  //调用栈
    if(root!=nullptr) call.push(root);  //首先介入root节点
    while(!call.empty()){
        TreeNode *t = call.top();
        call.pop();  //访问过的节点弹出
        if(t!=nullptr){
            if(t->right) call.push(t->right);  //右节点先压栈，最后处理
            if(t->left) call.push(t->left);
            call.push(t);  //当前节点重新压栈（留着以后处理），因为先序遍历所以最后压栈
            call.push(nullptr);  //在当前节点之前加入一个空节点表示已经访问过了
        }else{  //空节点表示之前已经访问过了，现在需要处理除了递归之外的内容
            res.push_back(call.top()->val);  //call.top()是nullptr之前压栈的一个节点，也就是上面call.push(t)中的那个t
            call.pop();  //处理完了，第二次弹出节点（彻底从栈中移除）
        }
    }
    return res;
}
```

#### 中序非递归

```c++
vector<int> inorderTraversal(TreeNode* root) {
    vector<int> res;
    stack<TreeNode*> call;
    if(root!=nullptr) call.push(root);
    while(!call.empty()){
        TreeNode *t = call.top();
        call.pop();
        if(t!=nullptr){
            if(t->right) call.push(t->right);
            call.push(t);  //在左节点之前重新插入该节点，以便在左节点之后处理（访问值）
            call.push(nullptr); //nullptr跟随t插入，标识已经访问过，还没有被处理
            if(t->left) call.push(t->left);
        }else{
            res.push_back(call.top()->val);
            call.pop();
        }
    }
    return res;
}
```

#### 后序非递归

```c++
vector<int> postorderTraversal(TreeNode* root) {
    vector<int> res;
    stack<TreeNode*> call;
    if(root!=nullptr) call.push(root);
    while(!call.empty()){
        TreeNode *t = call.top();
        call.pop();
        if(t!=nullptr){
            call.push(t);  //在右节点之前重新插入该节点，以便在最后处理（访问值）
            call.push(nullptr); //nullptr跟随t插入，标识已经访问过，还没有被处理
            if(t->right) call.push(t->right);
            if(t->left) call.push(t->left);
        }else{
            res.push_back(call.top()->val);
            call.pop();
        }
    }
    return res;
}
```

注意点

- 核心就是：根节点必须在右节点弹出之后，再弹出

#### DFS 深度搜索-从上到下

```c++
//前序遍历
void preorderHelper(TreeNode *node){
    if(node == nullptr) return;
    result.push_back(node->val);
    preorderHelper(node->left);
    preorderHelper(node->right);
}

vector<int> preorderTraversal1(TreeNode* root) {
    preorderHelper(root);
    return result;
}
```

#### DFS 深度搜索-从下向上（分治法）

```c++
//前序遍历
vector<int> inorderTraversal(TreeNode *node){
    return divideAndConquer(node);
}

vector<int> divideAndConquer(TreeNode *node){
    vector<int> result;
    if (node == nullptr) {
        return result;
    }

    vector<int> left = divideAndConquer(node->left);
    vector<int> right = divideAndConquer(node->right);

    result.push_back(node->val);
    result.insert(result.end(), left.begin(), left.end());
    result.insert(result.end(), right.begin(), right.end());

    return result;
}
```

注意点：

> DFS 深度搜索（从上到下） 和分治法区别：前者一般将最终结果通过指针参数传入，后者一般递归返回结果最后合并

#### BFS 层次遍历

```c++
//返回自上而下的结果

//BFS(广度优先搜索->队列)
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector <int>> ret;
    if (!root) return ret;

    queue <TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        int size = q.size();
        ret.push_back(vector <int> ());
        for (int i = 0; i < size; ++i) {
            auto node = q.front();
            q.pop();
            ret.back().push_back(node->val);
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }

    return ret;
}
```

#### DFS 层次遍历
```c++
//DFS(深度优先搜索->递归)
void addVector(TreeNode* root,int level)
{
  if(root == NULL)    return;
  if(res.size() == level)       res.resize(level+1);    //level表示层数，也对应二维数组的第一层索引，
  res[level].push_back(root->val);
  addVector(root->left,level+1);
  addVector(root->right,level+1);
}
```

### 分治法应用

先分别处理局部，再合并结果

适用场景

- 快速排序
- 归并排序
- 二叉树相关问题

分治法模板

- 递归返回条件
- 分段处理
- 合并结果

```c++
// 伪代码
class Solution {
    ResultType traversal(TreeNode root) {
        if (root == null) {
            // do something and return
        }

        // Divide
        ResultType left = traversal(root.left);
        ResultType right = traversal(root.right);

        // Conquer
        ResultType result = Merge from left and right

        return result;
    }
}
```

#### 典型示例

```c++
// 通过分治法遍历二叉树
class Solution {
    vector<int> prerderTraversal(TreeNode *root) {
        return divideAndConquer(root);
    }

    vector<int> divideAndConquer(TreeNode *node) {
        vector<int> result;
        if (node == nullptr) {
            return result;
        }
        // 分治
        vector<int> left = divideAndConquer(node->left);
        vector<int> right = divideAndConquer(node->right);
        // 合并结果
        result.push_back(node->val);
        result.insert(result.end(), left.begin(), left.end());
        result.insert(result.end(), right.begin(), right.end());
        return result;
    }
}
```

#### 归并排序  

```c++
void merge_sort(int q[], int l, int r)
{
    if (l >= r) return;

    int mid = l + r >> 1;
    merge_sort(q, l, mid);
    merge_sort(q, mid + 1, r);

    int k = 0, i = l, j = mid + 1;
    while (i <= mid && j <= r)
        if (q[i] <= q[j]) tmp[k ++ ] = q[i ++ ];
        else tmp[k ++ ] = q[j ++ ];

    while (i <= mid) tmp[k ++ ] = q[i ++ ];
    while (j <= r) tmp[k ++ ] = q[j ++ ];

    for (i = l, j = 0; i <= r; i ++, j ++ ) q[i] = tmp[j];
}
```

注意点

> 递归需要返回结果用于合并

#### 快速排序  

```c++
void quick_sort(int q[], int l, int r)
{
    if (l >= r) return;

    int i = l - 1, j = r + 1, x = q[l + r >> 1];
    while (i < j)
    {
        do i ++ ; while (q[i] < x);
        do j -- ; while (q[j] > x);
        if (i < j) swap(q[i], q[j]);
    }
    quick_sort(q, l, j), quick_sort(q, j + 1, r);
}
```

注意点：

> 快排由于是原地交换所以没有合并过程
> 传入的索引是存在的索引（如：0、length-1 等），越界可能导致崩溃

常见题目示例

#### maximum-depth-of-binary-tree

[maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

> 给定一个二叉树，找出其最大深度。

思路：分治法

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (!root) return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
```

#### balanced-binary-tree

[balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

> 给定一个二叉树，判断它是否是高度平衡的二叉树。

思路：分治法，左边平衡 && 右边平衡 && 左右两边高度 <= 1，
因为需要返回是否平衡及高度，要么返回两个数据，要么合并两个数据，
所以用-1 表示不平衡，>0 表示树高度（二义性：一个变量有两种含义）。

```c++
class Solution {
public:
    // 自底向上(后序遍历) O(n)
    // 每个节点都对比左右子树的高度
    int height(TreeNode* root) {
        if (!root) return 0;
        int leftHeight = height(root->left);
        int rightHeight = height(root->right);
        if (leftHeight == -1 || rightHeight == -1 || abs(leftHeight - rightHeight) > 1) {
            return -1;
        } else {
            return max(leftHeight, rightHeight) + 1;
        }
    }

    bool isBalanced(TreeNode* root) {
        return height(root) >= 0;
    }
};

class Solution {
public:
    // 自顶向下的递归 O(n^{2})
    // 二叉树最大深度
    int maxDepth(TreeNode* root) {
        if (!root) return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
    // 是否满足 一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1
    bool isBalanced(TreeNode* root) {
        if (!root) return true;
        return abs(maxDepth(root->left) - maxDepth(root->right)) <= 1 && isBalanced(root->left) && isBalanced(root->right);
        }
};
```

注意

> 一般工程中，结果通过两个变量来返回，不建议用一个变量表示两种含义

#### binary-tree-maximum-path-sum

[binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

> 给定一个**非空**二叉树，返回其最大路径和。

思路：分治法，分为三种情况：左子树最大路径和最大，右子树最大路径和最大，左右子树最大加根节点最大，需要保存两个变量：一个保存子树最大路径和，一个保存左右加根节点和，然后比较这个两个变量选择最大值即可

```c++
class Solution {
public:
    int maxSum = -2e9;
    
    int maxGain(TreeNode* node)
    {
        if (!node) return 0;

        int leftGain = max(maxGain(node->left), 0);
        int rightGain = max(maxGain(node->right), 0);

        // 左右加根节点和
        int priceNewPath = node->val + leftGain + rightGain;

        maxSum = max(maxSum, priceNewPath);
        // 左右子树最大路径和
        return node->val + max(leftGain, rightGain);
    }
    int maxPathSum(TreeNode* root) {
        maxGain(root);
        return maxSum;
    }
};
```

#### lowest-common-ancestor-of-a-binary-tree

[lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

思路：DFS，有左子树的公共祖先或者有右子树的公共祖先，就返回子树的祖先，否则返回根节点

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root || root == p || root == q) return root;

        TreeNode *left = lowestCommonAncestor(root->left, p, q);
        TreeNode *right = lowestCommonAncestor(root->right, p, q);

        if(left && right) return root;
        if(left) return left;
        if(right) return right;

        return nullptr;
    }
};
```

### BFS 层次应用

#### binary-tree-level-order-traversal

[binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

> 给你一个二叉树，请你返回其按  **层序遍历**  得到的节点值。 （即逐层地，从左到右访问所有节点）

思路：用一个队列记录一层的元素，然后扫描这一层元素添加下一层元素到队列（一个数进去出来一次，所以复杂度 O(logN)）

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

#### binary-tree-level-order-traversal-ii

[binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

> 给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

思路：在层级遍历的基础上，翻转一下结果即可

```c++
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> res;
        if(!root) return res;

        queue<TreeNode *> q;
        q.push(root);
        while(!q.empty()){
            int size = q.size();
            res.push_back(vector<int>());
            for(int i = 0; i < size; i++){
                TreeNode *node = q.front();
                q.pop();
                res.back().push_back(node->val);
                if(node->left) q.push(node->left);
                if(node->right) q.push(node->right);
            }
        }

        reverse(res.begin(), res.end());
        return res;
    }
};
```

#### binary-tree-zigzag-level-order-traversal

[binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

> 给定一个二叉树，返回其节点值的锯齿形层次遍历。Z 字形遍历

```c++
class Solution {
public:
    // 双端队列，左右遍历时候从不同位置插入
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> ans;
        if (!root) return ans;

        queue<TreeNode*> nodeQueue;
        nodeQueue.push(root);
        bool isOrderLeft = true;

        while (!nodeQueue.empty()) {
            deque<int> levelList;
            int size = nodeQueue.size();
            for (int i = 0; i < size; i ++) {
                auto node = nodeQueue.front();
                nodeQueue.pop();
                if (isOrderLeft) {
                    levelList.push_back(node->val);
                } else {
                    levelList.push_front(node->val);
                }
                if (node->left) {
                    nodeQueue.push(node->left);
                }
                if (node->right) {
                    nodeQueue.push(node->right);
                }
            }
            // deque to vector
            ans.push_back(vector<int>{levelList.begin(), levelList.end()});
            isOrderLeft = !isOrderLeft; // 下一次反序
        }

        return ans;
    }
};
```

### 二叉搜索树应用

#### validate-binary-search-tree

[validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。

思路 1：中序遍历，检查结果列表是否已经有序

思路 2：分治法，判断左 MAX < 根 < 右 MIN

```c++
// 中序遍历
class Solution {
public:
    long pre = LONG_MIN;
    bool isValidBST(TreeNode* root) {
        if(!root) return true;

        if(!isValidBST(root->left)) return false;

        if(root->val <= pre) return false;

        pre = root->val;

        return isValidBST(root->right);
    }
};
```

```c++
// 递归
bool helper(TreeNode* root, long long lower, long long upper) {
    if (root == nullptr) return true;
    if (root -> val <= lower || root -> val >= upper) return false;
    return helper(root -> left, lower, root -> val) && helper(root -> right, root -> val, upper);
}
bool isValidBST(TreeNode* root) {
    return helper(root, LONG_MIN, LONG_MAX);
}
```

#### insert-into-a-binary-search-tree

[insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。

思路：找到最后一个叶子节点满足插入条件即可

```c++
// DFS查找插入位置
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if(root == nullptr) return new TreeNode(val);

        if(root->val < val){ // 当前值大于根节点，往右边搜索
           root->right = insertIntoBST(root->right, val);
        }else{ // 当前值小于根节点，往左边搜索
           root->left = insertIntoBST(root->left ,val);
        }

        return root;
    }
};
```

## 总结

- 掌握二叉树递归与非递归遍历
- 理解 DFS 前序遍历与分治法
- 理解 BFS 层次遍历

## 练习

- [ ] [maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)
- [ ] [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)
- [ ] [binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
- [ ] [lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [ ] [binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
- [ ] [binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)
- [ ] [binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
- [ ] [validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)
- [ ] [insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
