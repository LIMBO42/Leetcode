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

