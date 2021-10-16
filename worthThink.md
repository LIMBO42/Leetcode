### 动态规划

#### LCP34（[LCP 34. 二叉树染色](https://leetcode-cn.com/problems/er-cha-shu-ran-se-UGC/)）

```c++
树形dp
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



#### DFS和位操作（[1239. 串联字符串的最大长度](https://leetcode-cn.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/)）

```c++
int DFS(vector<string>& arr, int str, int i) {
    if (i == arr.size()) {
        int count = 0;
        int tmpNum = str;
        for (int i = 0; i < 26; i++) {
            if (tmpNum & 1) count++;
            tmpNum = tmpNum >> 1;
        }
        return count;
    }
    uint32_t tmp = 0;
    for (int j = 0; j < arr[i].length(); j++) {
        int place = arr[i][j] - 'a';
        //有重复的字母，那么 tmp就不能加入
        if ((tmp >> place) & 1) {
            tmp = 0;
            break;
        }
        tmp = tmp | (1 << place);
    }
    //tmp和str，如果str中不含tmp中的元素可以加，否则只能不选他
    if (tmp & str || tmp==0) {
        return DFS(arr, str, i + 1);
    }
    else {
        uint32_t res = str | tmp;
        return max(DFS(arr, str, i + 1), DFS(arr, res, i + 1));
    }

}
int maxLength(vector<string>& arr) {
    //对于每一个字符串 有选和不选两种可能 要么选 要么不选
    //利用二进制来记录，将字符串转化成一个无符号整型
    //因为字符串本身肯定不能有重复的字符
    return DFS(arr, 0, 0);
 
}
```

该题的思路是对于每一个字符串都有两种选择，选择加入和不选它

由于字符串均由小写字母构成，且目标是评判是否有重复，因此位操作即可



#### 1524 和为奇数的连续子数组的数目（[1524. 和为奇数的子数组数目](https://leetcode-cn.com/problems/number-of-sub-arrays-with-odd-sum/)）

```c++
int numOfSubarrays(vector<int>& arr) {  
        //利用前缀和记录结尾的奇偶的个数
        long odd = 0, even = 1, sum=0;
        long res=0,mod=1000000007;
        for(int i=0;i<arr.size();i++){
            //加到当前为偶数
            //以当前数字结尾的连续数组必然是当前的odd个，因为要even-odd才行
            sum+=arr[i];
            if(sum%2==0){
                res = (res+odd)%mod;
                even++;
            }else{
                odd++;
                res = (res+even)%mod;
            }
        }
        return res;
    }
```

利用前缀和的思想，保证了连续子数组的和均保存在前缀和数组中，然后比较巧妙的利用了参数来保存以`nums[i]`结尾的数组中和为奇数，偶数的连续子数组数目，如果sum为偶数，那加上odd个（有odd种结尾的可能），如果sum为奇数，加上even个



#### [49. 字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)

```c++
vector<vector<string>> groupAnagrams(vector<string>& strs) {
	unordered_map<string,vector<string>> strMap;
    for(int i=0;i<strs.size();i++){
    	string key=strs[i];
        sort(key.begin(),key.end());
        strMap[key].push_back(strs[i]);
    }
    vector<vector<string>> res;
    for(auto it=strMap.begin();it!=strMap.end();it++){
    	res.push_back(it->second);
    }
    return res;
}
```



#### [1765. 地图中的最高点](https://leetcode-cn.com/problems/map-of-highest-peak/)

```C++
	vector<vector<int>> highestPeak(vector<vector<int>>& isWater) {
        int m=isWater.size();
        int n=isWater[0].size();
        vector<vector<int>> res(m,vector<int>(n,-1));
        vector<vector<bool>> is_modified(m,vector<bool>(n,false));
        queue<pair<int,int>> que;
        //多源点，将所有水域push进queue
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(isWater[i][j]==1){
                    que.push(make_pair(i,j));
                    res[i][j]=0;
                    is_modified[i][j]=true;
                    
                }
            }
        }
        vector<int> dir{-1,0,1,0,-1};
        while(!que.empty()){
            pair<int,int> place = que.front();
            que.pop();
            int num=res[place.first][place.second];
            
            for(int i=0;i<4;i++){
                int row = place.first+dir[i];
                int col = place.second+dir[i+1];
                //如果在图的范围且没有修改过
                if(row>=0&&row<m&&col>=0&&col<n&&is_modified[row][col]==false){
                    
                    res[row][col]=num+1;
                    is_modified[row][col]=true;
                    que.push(make_pair(row,col));
                }
            }
        }
        return res;
    }
```

思路主要是多源点的BFS，从多个源点同时出发，这样保证了发生冲突的时候保留比较小的那块

不可能相邻的高度差超过1，因为这样的话，比较小的那块应该继续蔓延的



#### [55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/)

```c++
    bool canJump(vector<int>& nums) {
        //站在某个位置能跳1-nums[i]之间的任何数，用maxLen记录能到达的最远的位置
        //如果出现有maxLen跳步到的位置返回false
        //nums[i]+i就是可能走到的最远的距离，之间的任何数都可能走到
        int maxLen=nums[0];
        for(int i=1;i<nums.size();i++){
            if(i>maxLen) return false;
            maxLen = max(nums[i]+i,maxLen);
        }
        return true;
    }
```

#### [114. 二叉树展开为链表](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

```C++
class Solution {
public:
    //想象一下如果从根开始将左节点置空的话不是很方便，因为子树也要进行对应的操作
    //因此考虑后序遍历
    void lastOrder(TreeNode *&root){
        if(root==NULL) return;
        lastOrder(root->left);
        lastOrder(root->right);
        
        if(root->left==NULL) return;
        if(root->right==NULL){
            root->right=root->left;
            root->left=NULL;
            return;
        }
        //左边不空的话，需要找到左子树的最右下的节点
        TreeNode *q = root->left;
        while(q->right!=NULL){
            q=q->right;
        }
        q->right = root->right;
        root->right = root->left;
        root->left=NULL;
    }
    void flatten(TreeNode* root) {
        lastOrder(root);
    }
};
```

#### [128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

```C++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if(nums.size()==0) return 0;
        unordered_set<int> tmpSet;
        for(int i=0;i<nums.size();i++){
            tmpSet.emplace(nums[i]);
        }
        int maxLen=1;
        //用空间换时间
        for(int i=0;i<nums.size();i++){
            //如果是不连续的第一个数
            //因此外层的循环是有限的，只是每次不连续的时候断开一次，时间复杂度kn
            if(tmpSet.find(nums[i]-1)==tmpSet.end()){        
                int tmpLen=1;
                //从nums[i]+1开始看有没有 连续的
                for(int j=nums[i]+1;;j++){       
                    if(tmpSet.find(j)==tmpSet.end()){
                        //没有找到
                        maxLen=max(maxLen,tmpLen);
                        break;
                    }else{
                        tmpLen++;
                    }
                }
            }
        } 
        return maxLen;
    }
};
```

#### [877. 石子游戏](https://leetcode-cn.com/problems/stone-game/)

```C++
class Solution {
public:
    bool stoneGame(vector<int>& piles) {
        //用dp[i][j]i-j之间先手和后手两个人的差
        //dp[i][j] = max(piles[i]-dp[i+1][j],piles[j]-dp[i][j-1])
        vector<vector<int>> dp(piles.size(),vector<int>(piles.size(),0));
        for(int i=piles.size()-1;i>=0;i--){
            for(int j=i;j<piles.size();j++){
                if(i==j) dp[i][j] = piles[i];
                else{
                    dp[i][j] = max(piles[i]-dp[i+1][j],piles[j]-dp[i][j-1]);
                }
            }
        }
        return dp[0][piles.size()-1] > 0;
    }
};
```

#### [238. 除自身以外数组的乘积](https://leetcode-cn.com/problems/product-of-array-except-self/)

```C++
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int start = 1, end = 1;
        vector<int> res(nums.size(),1);
        //前缀和的思路很容易想，常数空间需要从两边
        for(int i=0;i<nums.size();i++){
            res[i] *= start;
            start *= nums[i];
            res[nums.size()-1-i] *= end;
            end *= nums[nums.size()-1-i]; 
        } 
        return res;
    }
};
```

很容易想到用前缀和的方法来计算，但是如果要在常数空间内完成，需要考虑到两边乘积

分成两段就好理解很多

```C++
        //其实分成两段应该就能理解
        for(int i=0;i<nums.size();i++){
            res[i] *= start;
            start *= nums[i];
        }
        for(int i=nums.size()-1;i>=0;i--){
            res[i] *= end;
            end *= nums[i];
        }
        return res;
```





#### [331. 验证二叉树的前序序列化](https://leetcode-cn.com/problems/verify-preorder-serialization-of-a-binary-tree/)

```C++
bool isValidSerialization(string preorder) {
    istringstream is(preorder);
    string tmp;
    stack<char> st;
    while (getline(is, tmp, ',')) {
        st.push(tmp[0]);
        if (st.size() >= 3) {
            char ch1 = st.top();
            st.pop();
            char ch2 = st.top();
            st.pop();
            char ch3 = st.top();
            st.pop();
            if (ch1 == '#' && ch2 == '#' && ch3 != '#') {
                st.push('#');
                while (st.size() >= 3) {
                    char ch1 = st.top();
                    st.pop();
                    char ch2 = st.top();
                    st.pop();
                    char ch3 = st.top();
                    st.pop();
                    if (ch1 == '#' && ch2 == '#' && ch3 != '#') {
                        st.push('#');
                    }
                    else {
                        st.push(ch3);
                        st.push(ch2);
                        st.push(ch1);
                        break;
                    }
                }
            }
            else {
                st.push(ch3);
                st.push(ch2);
                st.push(ch1);
            }
        }
    }
    
    if (st.size() == 1 && st.top() == '#') return true;
    return false;
}
```

主要是不断消去的思路





#### [剑指 Offer 56 - I. 数组中数字出现的次数](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)

```c++
class Solution {
public:
    vector<int> singleNumbers(vector<int>& nums) {
        //所有数进行异或
        //出现两次的数字异或的结果是 0 
        //因此 结果是 那两个只出现一次的数字的异或
        int tmpOr = 0;
        for(int i=0;i<nums.size();i++){
            tmpOr = tmpOr ^ nums[i];
        }
        //从右向左 看哪一位为1
        int count = 0;
        while(tmpOr!=0){
            int tmp = tmpOr % 2;
            if(tmp == 1) break;
            tmpOr = tmpOr/2;
            count++;
        }
        int group1 = 0;
        int group2 = 0;
        for(int i=0;i<nums.size();i++){
            int num = 0;
            int tmp;
            int tmpOr = nums[i];
            while(num<=count){
                tmp = tmpOr % 2;
                tmpOr = tmpOr / 2;
                num++;
            }
            if(tmp==0){
                group1 = group1^nums[i];
            }else if(tmp == 1){
                group2 = group2^nums[i];
            }
        }
        return vector<int>{group1,group2};
    }
};
```

> 相同的数异或为0，不同的异或为1。0和任何数异或等于这个数本身。
>
> 所以，数组里面所有数异或 = 目标两个数异或 。 由于这两个数不同，所以异或结果必然不为0。
>
> 假设数组异或的二进制结果为10010，那么说明这两个数从右向左数第2位是不同的
>
> 那么可以根据数组里面所有数的第二位为0或者1将数组划分为2个。
>
> 这样做可以将目标数必然分散在不同的数组中，而且相同的数必然落在同一个数组中。
>
> 这两个数组里面的数各自进行异或，得到的结果就是答案



#### [865. 具有所有最深节点的最小子树](https://leetcode-cn.com/problems/smallest-subtree-with-all-the-deepest-nodes/)

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
    int maxDepth = -1;
    unordered_map<TreeNode*,int> map;
    unordered_map<TreeNode*,TreeNode*> fatherMap;
    int count=0;
    void preOrder(TreeNode *root,int depth){
        if(root == nullptr) return;
        map[root] = depth;
        maxDepth = max(maxDepth,depth);
        
        if(root->left!=nullptr)
            fatherMap[root->left] = root;
        if(root->right!=nullptr)
            fatherMap[root->right] = root;
        preOrder(root->left,depth+1);
        preOrder(root->right,depth+1);
    }
    
    //看能不能找到到
    bool judge(TreeNode* root, TreeNode* node){
        if(root == nullptr) return false;
        if(root == node) return true;
        if(judge(root->left,node)||judge(root->right,node)) return true;
        return false;
    }

    TreeNode* findFather(TreeNode* root, TreeNode*root1,TreeNode* root2){
        //如果一个在左边 一个在右边
        bool flag1left = judge(root->left,root1);
        bool flag1right = judge(root->right,root1);
        bool flag2left = judge(root->left,root2);
        bool flag2right = judge(root->right,root2);
        if(flag1left&&flag2right) return root;
        else if(flag1right&&flag2left) return root;
        else if(flag1left&&flag2left) return findFather(root->left,root1,root2);
        else if(flag1right&&flag2right) return findFather(root->right,root1,root2);
        return root;
    }
    
    TreeNode* subtreeWithAllDeepest(TreeNode* root) {
        preOrder(root,0);
        
        stack<TreeNode*> vec;
        for(auto it = map.begin();it!=map.end();it++){
            //如果遇到的是最大
            if(it->second == maxDepth){
                count++;
                vec.push(it->first);
            }
        }
        if(vec.size()==1) return vec.top();
        
        while(vec.size()>=2){
            TreeNode* node1=vec.top();
            vec.pop();
            TreeNode* node2=vec.top();
            vec.pop();
            
            //找这两个节点的公共祖先
            vec.push(findFather(root,node1,node2));
        }
        return vec.top();
    }
};
```

如何去寻找两个节点的共同祖先？

如果两个节点均在左边子树，或者均在右边子树，则需要说明共同祖先在左子树或者右子树

如果两个节点分别在左右子树，则共同祖先就是该节点

如何判断在哪个子树中？利用递归去寻找。



#### [560. 和为 K 的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

```C++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        //计算前缀和
        //end - start
        unordered_map<int,int> sumMap;
        int sum=0;
        sumMap[0]=1;
        int count=0;
        //记录出现几次
        //如果当前是sum，前面的map中有出现sum-k
        //说明从sum-k的那段到sum的这一段为所要求的的连续子数组
        for(int i=0;i<nums.size();i++){
            sum+=nums[i];
            count+=sumMap[sum-k];
            sumMap[sum]++;
        }
        
        return count;

    }
};
```

思路：要求连续数组和为k，考虑前缀和，如果当前的和为`sum`，并且前面有前缀和为`sum-k`，就说明存在一段数组和为k。



#### [524. 通过删除字母匹配到字典里最长单词](https://leetcode-cn.com/problems/longest-word-in-dictionary-through-deleting/)

```C++
class Solution {
public:
    //判断str2是不是str1的子序列
    //双指针
    bool judge(string &str1, string &str2){
        int cur1=0,cur2=0;
        while(cur2<str2.length()&&cur1<str1.length()){
            if(str1[cur1]==str2[cur2]){
                //cur1++;
                cur2++;
            }
            cur1++;
        }
        if(cur2==str2.length()) return true;
        return false;
    }
    bool static compare(const string &str1,const string &str2){
        if(str1.length()>str2.length()) return true;
        else if(str1.length()<str2.length()) return false;
        return str1<str2;
        
    }
    string findLongestWord(string s, vector<string>& dictionary) {
        
        vector<vector<int>> map(dictionary.size(),vector<int>(26,0));
        vector<string> res;
        for(int i=0;i<dictionary.size();i++){
            
            if(judge(s,dictionary[i])){
                res.push_back(dictionary[i]);
            }
        }
        // s中的每个字符均的值均要>=
        if(res.size()==0) return "";
        sort(res.begin(),res.end(),compare);
        return res[0];
    }
};
```

判断一下`judge(str1,str2)`；`str2`是不是`str1`的子序列，思路是根据`str2`中的必然在`str1`中会出现， 那出现一个字母就在`str1`中不断找，直到找到相等的
