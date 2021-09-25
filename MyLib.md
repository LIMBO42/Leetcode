```c++
//判断str1是否包含str2
str1.find(str2)!=str::npos
    
    
//将string转化为vector
//将string转化为输入流
istringstream iss(i);
string temp;
vector<string> vs;
//截断
while(getline(iss,temp,','))
	vs.push_back(temp);


//my_lower_bound是lower_bound(vec.begin(),vec.end(),target)
//返回的是大于等于target的值的位置
//对应的是upper_bound返回大于target的位置
int my_lower_bound(vector<int> &vec, int first, int last, int val){
    while(first<last){
        int mid = first + (last-first)/2;
        if(vec[mid]<val) first = mid + 1;
        else last = mid;
    }
    return first;
}
```

<img src="https://typora-1306385380.cos.ap-nanjing.myqcloud.com/img/image-20210923095304156.png" alt="image-20210923095304156" style="zoom:67%;" />

也就是说一个`lower_bound`函数可以处理几乎所有的情况了(`upper_bound`只需要向后找几个元素，当然如果数据量大可能比较慢)



#### **左闭右开**

左闭右开的思想在很多方面都有体现，比如`vec.end()`，`for range`，`for int i=0;i<vec.size();i++`，`slice`

<img src="https://typora-1306385380.cos.ap-nanjing.myqcloud.com/img/image-20210923101559167.png" alt="image-20210923101559167" style="zoom:67%;" />

