### 动态规划

#### 树形dp

```c++
LCP34
class Solution {
public:
    vector<int> DFS(TreeNode *root, int k){
        //对于每一个节点都有一棵树，以此计算f0-fk
        vector<int >f(k+1,0);
        if(root == NULL) return f;
        auto l = DFS(root->left,k);
        auto r = DFS(root->right,k);
        //没有染色：左右节点随便什么颜色
        f[0] = *max_element(l.begin(),l.end()) + *max_element(r.begin(),r.end());
        //f[i]，i可以取到k代表，root染色且最多k个节点相连，那么左边可能有j个染色的节点右边有i-1-j个染色的节点
        //因为root当前节点染色
        for(int i=1;i<=k;i++){
            for(int j=0;j<i;j++){
                f[i] = max(root->val + l[j] + r[i-1-j],f[i]);
            }
        }
        return f;
    }
    int maxValue(TreeNode* root, int k) {
        auto res = DFS(root,k);
        return *max_element(res.begin(),res.end());
    }
};
```

该题的思路是状态的设计：dp[i]表示染色的有i个，那么考虑当前节点的染色情况，如果当前节点没有染色，那么左右节点随便，如果当前节点染色了，那么就要考虑左边能够染色的j个右边能染色的i-j-1