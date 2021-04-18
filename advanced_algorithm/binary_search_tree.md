# 二叉搜索树

## 定义

- 每个节点中的值必须大于（或等于）存储在其左侧子树中的任何值。
- 每个节点中的值必须小于（或等于）存储在其右子树中的任何值。

## 应用

[validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> 验证二叉搜索树

```c++
class Solution {
public:
    typedef long long LL;
    bool helper(TreeNode* root, LL lower, LL upper)
    {
        if (root == nullptr) return true;

        if (root->val <= lower || root->val >= upper) return false;

        return helper(root->left, lower, root->val) && helper(root->right, root->val, upper);
    }
    bool isValidBST(TreeNode* root) {
        return helper(root, LONG_MIN, LONG_MAX);
    }
};
```

[insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 保证原始二叉搜索树中不存在新值。

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

[delete-node-in-a-bst](https://leetcode-cn.com/problems/delete-node-in-a-bst/)

> 给定一个二叉搜索树的根节点 root 和一个值 key，删除二叉搜索树中的  key  对应的节点，并保证二叉搜索树的性质不变。返回二叉搜索树（有可能被更新）的根节点的引用。

```c++
class Solution {
public:
    TreeNode* deleteNode(TreeNode* root, int key) 
    {
        if (!root) return nullptr;
        // 去右子树删除
        if (key > root->val) root->right = deleteNode(root->right, key);
        // 去左子树删除
        else if (key < root->val) root->left = deleteNode(root->left, key);
        else // 当前节点就是要删除的节点
        {
            if (! root->left) return root->right; // 情况1，欲删除节点无左子
            if (! root->right) return root->left;  // 情况2，欲删除节点无右子
            TreeNode* node = root->right;           // 情况3，欲删除节点左右子都有 
            while (node->left) node = node->left; // 寻找欲删除节点右子树的最左节点
            // 将欲删除节点的左子树成为其右子树的最左节点的左子树
            node->left = root->left;
            // 欲删除节点的右子顶替其位置，节点被删除
            root = root->right;
        }
        return root;    
    }
};
```

[balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

> 给定一个二叉树，判断它是否是高度平衡的二叉树。

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
```

## 练习

- [ ] [validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)
- [ ] [insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
- [ ] [delete-node-in-a-bst](https://leetcode-cn.com/problems/delete-node-in-a-bst/)
- [ ] [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)
