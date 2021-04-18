# 排序

## 常考排序

### 快速排序

```c++
quick_sort(nums, 0, nums.size() - 1);

// 思路：把一个数组分为左右两段，左段小于右段
void quick_sort(vector<int>& nums, int l, int r)
{
    if (l >= r) return; // 终止条件

    int i = l - 1, j = r + 1; // 左端点的左边右端点的右边开始遍历
    int x = nums[(l + r) >> 1]; // 可以取随机数, 这边是数组里的数
    while (i < j)
    {
        do i ++; while (nums[i] < x); // i左边都是小于x的数
        do j --; while (nums[j] > x); // j右边都是大于x的数
        if (i < j) swap(nums[i], nums[j]); // 当前nums[i]大于x，nums[j]小于x，如果i在j的左边则交换两个数的位置
    }
    // 递归的对剩余部分进行排序
    quick_sort(nums, l, j);
    quick_sort(nums, j + 1, r);
}
```

### 归并排序

```c++
merge_sort(nums, 0, nums.size() - 1);

// 分治，先排序，再合并, 需要一个额外数组
void merge_sort(vector<int>& nums, int l, int r)
{
    if (l >= r) return; // 终止条件

    int mid = (l + r) >> 1; // 这边是下标
    // 先排序
    merge_sort(nums, l, mid);
    merge_sort(nums, mid + 1, r);

    // 合并为一段
    int k = 0, i = l, j = mid + 1;
    vector<int> tmp(r - l + 1, 0);
    while (i <= mid && j <= r)
    {
        // 按照大小顺序合并
        if (nums[i] <= nums[j]) tmp[k ++] = nums[i ++];
        else tmp[k ++] = nums[j ++];
    }
    // 剩余的部分合并
    while (i <= mid) tmp[k ++] = nums[i ++];
    while (j <= r) tmp[k ++] = nums[j ++];

    // 把临时数组里的值放回原数组
    for (int i = l, j = 0; i <= r; i ++, j ++) nums[i] = tmp[j];
}
```

### 堆排序

**堆的概念**

堆是用数组表示的完美二叉树 complete binary tree

小根堆: 每个父节点的值都小于左右儿子

大根堆: 每个父节点的值都大于左右儿子

堆的实现(小根堆):
* 堆的存储: 1维数组, 0为根节点, 父节点是x的话, 左儿子为2x+1, 右儿子为2x+2
* down(x)：把一个数变大了, 需要维持堆的定义的话就需要与左右儿子中小的值进行交换

* up(x)：把一个数变小了，需要维持堆的定义的话需要与父节点(大于当前数的话)进行交换

* 插入一个数: heap[ ++ size] = x; up(size);
* 求集合中最小值: heap[0];
* 删除最小值: heap[0] = heap[size]; size --; down(0);
* 删除任意一个元素: heap[k] = heap[size]; size --; down(k); up(k);
* 修改任意一个元素: heap[k] = x; down(k); up(k);

```c++
heap_sort(nums);

// 小根堆
void down(vector<int>& heap, int size, int u)
{
    int t = u;
    // 如果左儿子存在并且左儿子的值小于当前的值，交换
    if (u * 2 + 1 <= size && heap[u * 2 + 1] < heap[t]) t = u * 2 + 1;
    // 如果右儿子存在并且右儿子值小于当前的值，交换
    if (u * 2 + 2 <= size && heap[u * 2 + 2] < heap[t]) t = u * 2 + 2;
    if (u != t)
    {
        swap(heap[u], heap[t]);
        down(heap, size, t);
    }
}
void heap_sort(vector<int>& nums)
{
    int size = nums.size() - 1;
    // 建堆(小根堆) O(n)
    for (int i = n / 2; i >= 0; i --) down(nums, size, i);

    // 交换堆顶和末尾的数然后从堆顶down(0)
    // 长度需要-1
    int len = nums.size() - 1;
    for (int i = size; i > 0; i --)
    {
        swap(nums[0], nums[i]);
        len --;
        down(nums, len, 0);
    }
    
    // 正序的话用大根堆或者小根堆后反序
    reverse(nums.begin(), nums.end());
}
```

## 参考

[十大经典排序](https://www.cnblogs.com/onepixel/p/7674659.html)

## 练习

- [ ] 手写快排、归并、堆排序
