#### 1008 前序遍历构造二叉搜索树

题目是给出二叉搜索树的前序遍历，要求构造出二叉搜索树

思路：通过二叉搜索树左右节点的大小关系，将数组划分成不同的区域，递归处理

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    void Mypreorder(TreeNode *&root, vector<int>& preorder, int start,int end){
        if(start>end){
            root=nullptr;
            return;
        }
        if(start==end) {
            root->val = preorder[start];
            root->left=nullptr;
            root->right=nullptr;
            return;
        }
        root->val = preorder[start];
        root->left=new TreeNode;
        root->right=new TreeNode;
        int leftStart = start+1;
        int rightStart;
        bool is_have_larger=false;
        for(rightStart = leftStart; rightStart < preorder.size(); rightStart++){
            if(preorder[rightStart]>preorder[start]){
                is_have_larger=true;
                Mypreorder(root->left,preorder,leftStart,rightStart-1);
                Mypreorder(root->right,preorder,rightStart,end);
                return;
            }
        }
        if(!is_have_larger){
            root->right=nullptr;
            Mypreorder(root->left,preorder,leftStart,end);
            return;
        }
        
    }
    TreeNode* bstFromPreorder(vector<int>& preorder) {
        TreeNode* root=new TreeNode;
        Mypreorder(root,preorder,0,preorder.size()-1);
        return root;
    }
};
```

