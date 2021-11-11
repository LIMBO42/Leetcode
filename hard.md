#### 64匹马，8条赛道，如何比赛才能在最小次数找到最快的4匹

先分成8组组内进行比较，再将每组第一名进行比较，总共九次

列出树状图，其中距离第一名超过4的一定不在前四，这样可能的前四就是

2,3,4,9,10,11,17,18,25共9匹马再进行两次赛跑即可

共十一次赛跑可决出前四名

<img src="C:\Users\LIMBO\AppData\Roaming\Typora\typora-user-images\image-20211002142238490.png" alt="image-20211002142238490" style="zoom:33%;" />





#### [146. LRU 缓存机制](https://leetcode-cn.com/problems/lru-cache/)

有BUG未全部通过测试

```C++
class LRUCache {
private:
struct linkList {
	int key;
	int val;
	linkList* pre;
	linkList* next;
};
	//对于每一个key获得对应的node
	unordered_map<int, linkList*> cache;
	linkList* dumHead;
	linkList* dumTail;
	int capacity_;
	//int tailKey;
	//从head 到 tail 按使用率排序
	void remove(linkList* p) {
		p->pre->next = p->next;
		p->next->pre = p->pre;
		//delete p;
	}
	//add
	void addHead(int key,int val) {
		linkList* p = new linkList;
		p->val = val;
		p->key = key;
		p->pre = dumHead;
		p->next = dumHead->next;
		dumHead->next->pre = p;
		dumHead->next = p;
	}
	void removeTail() {
		dumTail->pre = dumTail->pre->pre;
		dumTail->pre->next = dumTail;
		
	}

public:
	LRUCache(int capacity) {
		capacity_ = capacity;
		dumHead = new linkList;
		dumTail = new linkList;
		dumHead->next = dumTail;
		dumTail->pre = dumHead;
	}
	int get(int key) {
		auto it = cache.find(key);
		if (it == cache.end()) return -1;
		else {
			linkList* p = it->second;
			int val = p->val;
			remove(p);
			addHead(key,val);
			return val;
		}
	}
	void put(int key, int value) {
		//如果关键字存在
		auto it = cache.find(key);
		if (it != cache.end()) {
			linkList* p = it->second;
			//int val = p->val;
			remove(p);
			addHead(key,value);
            //注意map对应的node需要修改
            it->second = dumHead->next;
		}
		//关键字不存在
		else {
			//容量达到上限，需要删除最久没有使用的值，即删除尾部
			if (cache.size() == capacity_) {
                //map需要根据key值删除所以才需要加上key
				cache.erase(dumTail->pre->key);
				removeTail();
			}
			//插入
			addHead(key,value);
			cache.emplace(key, dumHead->next);
		}
	}
};
```

基本思路是：由于`LRU`要求有序链表还需要`map`来保证`O(1)`时间复杂度，考虑从头到尾表示常用与否

`put()`需要考虑关键字存在的时候更改`value`的值，还需要将`node`移到最前面；如果关键字不存在且容量到达上限，则需要删除尾部的`node`，容量没有到达上限，则需要在头部加上`node`。

这里的`key value`我感觉是`key`是页面号，而`value`是对应的页框号



#### [75. 颜色分类](https://leetcode-cn.com/problems/sort-colors/)

没有想明白为什么0和2一个要++，一个不需要

```c++
class Solution {
public:
    
    void sortColors(vector<int>& nums) {
        if(nums.size()==1) return ;
        int zeros=0;
        int twos=nums.size()-1;
        //[0,zeros)=0;
        //[zeros,i)=1;
        //(twos,len-1]=2;

        //i作为遍历的迭代
        //如果nums[i]==0要与第一个不是0的交换
        //如果nums[i]==1则不动
        //如果nums[i]==2则与从后至前第一个不是2的交换

        //那终止条件是什么？
        //i<=twos
        int i=0;
        while(twos>=0&&nums[twos]==2){
            twos--;
        }
        while(zeros<nums.size()&&nums[zeros]==0){
            zeros++;
        }
        i=zeros;
        while(i <= twos){
            
            if(nums[i]==0){
                swap(nums[zeros],nums[i]);
                //经过swap之后 放到nums[i]的是1/2
                zeros++;
                i++;
                
            }else if(nums[i]==1){
                i++;
            }else if(nums[i]==2){
                swap(nums[i],nums[twos]);
                //i++;
                //经过swap之后，放到nums[i]的可能是0/1:此时i不能++,还需要对可能的0进行操作
                twos--;
                
            }
        }
    }
};
```

#### [208. 实现 Trie (前缀树)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)

```c++
class Trie {
    struct Node{
        Node* word[26];
        bool isWorld;
    };
private:
    Node* root;
    //Node* pre;
    Node* cur;
public:
    Trie() {
        root = new Node;
        memset(root->word, 0, 26 * sizeof(Node*));
        root->isWorld = false;
        cur = root;
    }

    void insert(string word) {
        cur = root;
        for (int i = 0; i < word.length(); i++) {
            //如果当前的字母没有出现
            int index = word[i] - 'a';
            if (cur->word[index] == nullptr) {
                Node* next = new Node;
                memset(next->word, 0, 26 * sizeof(Node*));
                next->isWorld = false;
                cur->word[index] = next;
            }
            /*
            if (i == word.length() - 1) {
                cur->isWorld = true;
            }*/
            cur = cur->word[index];
        }
        cur->isWorld = true;
    }

    bool search(string word) {
        cur = root;
        for (int i = 0; i < word.length(); i++) {
            int index = word[i] - 'a';
            if (cur->word[index] == nullptr) return false;
            cur = cur->word[index];
        }
        if (cur->isWorld == true) return true;
        return false;
    }

    bool startsWith(string prefix) {
        cur = root;
        for (int i = 0; i < prefix.length(); i++) {
            int index = prefix[i] - 'a';
            if (cur->word[index] == nullptr){
                return false;
            }
            cur = cur->word[index];
        }
        //if (cur->isWorld == true) return true;
        return true;
    }
};
```

这个设计主要就是要想到`Node* word[26]`。

还有一个问题就是设计的时候应该是`\0`的位置置`bool`为`true`，否则判断前缀的时候`jam,jan`就判断不出来

#### [148. 排序链表](https://leetcode-cn.com/problems/sort-list/)

```c++
#include<iostream>
#include<istream>
#include<sstream>
#include<string>
#include<deque>
using namespace std;
struct ListNode {
    int val;
    ListNode* next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode* next) : val(x), next(next) {}
};

//快慢指针找链表终点
ListNode* searchMid(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;
    ListNode* pre = head;
    while (fast != nullptr && fast->next != nullptr) {
        pre = slow;
        slow = slow->next;
        fast = fast->next->next;
    }
    return pre;
}

//合并两个有序链表，返回的是有头指针的链表
ListNode* merge(ListNode* list1, ListNode* list2) {
    ListNode* head = new ListNode;
    head->next = nullptr;
    ListNode* pre = head;
    while (list1 != nullptr && list2 != nullptr) {
        int num; 
        if (list1->val < list2->val) {
            num = list1->val;
            list1 = list1->next;
        }
        else {
            num = list2->val;
            list2 = list2->next;
        }
        ListNode* tmp = new ListNode;
        tmp->val = num;
        tmp->next = nullptr;
        pre->next = tmp;
        pre = pre->next;
    }
    while (list1 != nullptr) {
        ListNode* tmp = new ListNode;
        tmp->val = list1->val;
        tmp->next = nullptr;
        pre->next = tmp;
        pre = pre->next;
        list1 = list1->next;
    }
    while (list2 != nullptr) {
        ListNode* tmp = new ListNode;
        tmp->val = list2->val;
        tmp->next = nullptr;
        pre->next = tmp;
        pre = pre->next;
        list2 = list2->next;
    }
    return head;
}

void printList(ListNode* head) {
    for (ListNode* p = head; p != nullptr; p = p->next) {
        cout << p->val << " ";
    }
    cout << endl;
}

ListNode* createList() {
    string str;
    cin >> str;
    istringstream iss(str);
    string temp;
    ListNode* head = new ListNode;
    head->next = nullptr;
    deque<int> tmpQue;
    while (getline(iss, temp, ',')) {
        tmpQue.push_back(atoi(temp.c_str()));
    }
    while (!tmpQue.empty()) {
        ListNode* p = new ListNode;
        p->val = tmpQue.back();
        tmpQue.pop_back();
        p->next = head->next;
        head->next = p;
    }
    return head;
}

//递归
ListNode* func(ListNode* head) {
    //只有一个元素或者就是空
    if (head == nullptr || head->next == nullptr) return head;
    //找到中点
    ListNode* pre = searchMid(head);
    ListNode* mid = pre->next;
    pre->next = nullptr;
    //递归完了都是有序的
    ListNode* list1 = func(head);
    ListNode* list2 = func(mid);
    ListNode* res = merge(list1, list2);
    return res->next;
}

int main()
{
    /*
    ListNode* head1 = createList();
    ListNode* head2 = createList();
    ListNode* head = merge(head1->next, head2->next);
    printList(head->next);
    */
    ListNode* head = createList();
    ListNode* res = func(head->next);
    printList(res);
    
    //cout << searchMid(head->next)->val << endl;
    
	return 0;
}
```

首先`nlogn`的排序可能有快排，归并，堆排序，其中快排需要用到元素的交换和位置信息，堆排序同样需要位置信息，因此只有归并最有可能。

但归并要找链表中点，怎么找？快慢指针

递归合并