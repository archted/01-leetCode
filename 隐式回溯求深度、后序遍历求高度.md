
## 题目：求二叉树最大深度   
二叉树节点的深度：根节点到该节点路径之间的节点个数
### 解法一：从上往下求，采用先序遍历
#### a.显示回溯
```java
class Solution {
public:
    int result;
    void getDepth(TreeNode* node, int depth) {
        result = depth > result ? depth : result; // 中

        if (node->left == NULL && node->right == NULL) return ;

        if (node->left) { // 左
            depth++;    // 深度+1
            getDepth(node->left, depth);
            depth--;    // 回溯，深度-1
        }
        if (node->right) { // 右
            depth++;    // 深度+1
            getDepth(node->right, depth);
            depth--;    // 回溯，深度-1
        }
        return ;
    }
    int maxDepth(TreeNode* root) {
        result = 0;
        if (root == 0) return result;
        getDepth(root, 1);
        return result;
    }
};   
```
#### b.隐式回溯
```java
class Solution {
    public:
    int result;

    void getDepth(TreeNode* node, int depth) {
            result = depth > result ? depth : result; // 中
            if (node->left == NULL && node->right == NULL) return ;
            if (node->left) { // 左
                getDepth(node->left, depth + 1);
            }
            if (node->right) { // 右
                getDepth(node->right, depth + 1);
            }
            return ;
        }

    int maxDepth(TreeNode* root) {
        result = 0;
        if (root == 0) return result;
        getDepth(root, 1);
        return result;
    }
}
```
### 解法二：求二叉树的最大深度就是求根节点的最大高度  
求二叉树最大高度：从下往上遍历，后序遍历
```java
int getDepth（TreeNode* node）{
    if(node == null){
        return 0;
    }
	int leftDepth = getDepth(node.left);
	int rightDepth = getDepth(node.right);
	int depth = Math.max(leftDepth,rightDepth)+1;
	return depth;
}
```

