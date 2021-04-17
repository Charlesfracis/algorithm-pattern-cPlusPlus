# C++ 快速入门

## 基本语法

[c++基本语法](https://www.runoob.com/cplusplus/cpp-tutorial.html)

## STL库


```c++
vector, 变长数组， 倍增的思想(减小申请空间次数)
    size() 返回元素个数
    empty() 返回是否为空
    clear() 清空
    front() / back()
    push_back() / pop_back()
    begin() / end()
    [] 随机选取
    支持比较运算， 按字典序
    拷贝: vector<int> bk(list);

pair<int, int>
    first, 第一个元素
    second, 第二个元素
    支持比较运算， 按字典序， 以first为第一个字典序

string, 字符串
    size() / length()
    empty()
    clear()
    substr(idx, size)
    printf("%s", c_str())

queue, 队列
    size()
    empty()
    push() 队尾插入一个元素
    front() 返回队头元素
    back() 返回队尾元素
    pop() 弹出队头元素

priority_queue, 堆(优先队列)， 默认是大根堆
    size()
    empty()
    push() 向堆中插入一个元素
    top() 返回堆顶元素
    pop() 弹出堆顶元素
    priority_queue<int, vector<int>, greater<int>> heap; // 小跟堆定义

stack, 栈
    size()
    empty()
    push() 向栈顶插入一个元素
    top() 返回栈顶元素
    pop() 弹出栈顶元素

deque, 双端队列
    size()
    empty()
    clear()
    front() 返回第一个元素
    back() 返回最后一个元素
    push_back() / pop_back()
    push_front() / pop_front()
    begin() / end()
    [] 随机选取

set, map, multiset, multimap, 基于平衡二叉树(红黑树), 动态维护有序数列
    size()
    empty()
    clear()
    begin() / end() ++, -- 返回前驱和后继

    set/multiset
        insert() 插入一个数
        find() 查找一个数
        count() 返回某一个数的个数
        erase()
            (1) 输入是一个数，删除所有x O(k + logn)
            (2) 输入一个迭代器，删除这个迭代器
        lower_bound() / upper_bound()
            lower_bound(x) 返回大于等于x的最小的数的迭代器
            upper_bound(x) 返回大于x的最小的数的迭代器 

    map/multimap
        insert() 插入的数是一个pair
        erase() 输入的参数是pair或者迭代器
        find()
        [] map<string, int> 时间复杂度 O(logn)
        lower_bound() / upper_bound()

unordered_set, unordered_map, unordered_multiset, unordered_multimap, 哈希表
    和上面类似， 增删改查时间复杂度是O(1)
    不支持 lower_bound() / upper_bound()， 迭代器的++, --

bitset, 压位
    bitset<10000> S;
    ~, &, |, ^
    >>, <<
    ==. !=
    []
    count() 返回有多少个1
    any() 判断是否至少有一个1
    none() 是否全为0
    set() 把所有位置成1
    set(k, v) 将第k位变成v
    reset() 把所有位置成0
    flip() 等价于取反
    flip(k) 第k位置取反

```

### 标准库

**sort**

```c++
// INT, 字符串排序
sort(nums.begin(), nums.end());
// 自定义排序
// bool  compare(类型名称 a,类型名称 b){return a>b;}

bool compare(const int &odd1,const int &odd2)
{
	return odd1>odd2;

sort(nums.begin(), nums.end(), compare);
```

### 常用技巧

类型转换

```c++
// byte转数字
char s = "12345";  // s[0] 类型是char
int num =s[0]-'0'; // 1

// 字符串转数字
int num = atoi(const char*);
int num = stoi(string*);
```

## 刷题注意点

- leetcode 中，全局变量不要当做返回值，否则刷题检查器会报错
