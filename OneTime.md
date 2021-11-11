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

#### 950 递增顺序显示纸牌

思路：从最后一步开始模拟，每次都是将最后一张移动到第一张，然后按顺序防止纸牌

```C++
class Solution {
public:
    static bool compare(int &a,int &b){
        return a>b;
    }
    vector<int> deckRevealedIncreasing(vector<int>& deck) {
        if(deck.size()==0) return vector<int>{};
        else if(deck.size()==1) return vector<int>{deck[0]};
        sort(deck.begin(),deck.end(),compare);
        deque<int> res;
        res.push_back(deck[0]);
        res.push_front(deck[1]);
        for(int i=2;i<deck.size();i++){
            int tmp = res.back();
            res.pop_back();
            res.push_front(tmp);
            res.push_front(deck[i]);
        }
        vector<int> result;
        while(res.empty()==false){
            result.push_back(res.front());
            res.pop_front();
        }
        return result;
    }
    
};
```





#### [1110. 删点成林](https://leetcode-cn.com/problems/delete-nodes-and-return-forest/)

```C++
class Solution {
public:
    vector<TreeNode*> res;
    void lastOrder(TreeNode* &root, vector<int>& to_delete){
        if(root==nullptr) return;
        lastOrder(root->left,to_delete);
        lastOrder(root->right,to_delete);
        if(find(to_delete.begin(),to_delete.end(),root->val)!=to_delete.end()){
            if(root->left!=nullptr) res.push_back(root->left);
            if(root->right!=nullptr) res.push_back(root->right);
            root=nullptr;
        }

    }
    vector<TreeNode*> delNodes(TreeNode* root, vector<int>& to_delete) {
        lastOrder(root,to_delete);
        if(root!=nullptr) res.push_back(root);
        return res;
    }
};
```

就主要要想到是后序遍历，因为要先将子节点处理好了才能放进`vector`中
