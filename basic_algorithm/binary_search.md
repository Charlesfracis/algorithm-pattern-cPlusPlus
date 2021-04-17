# 二分搜索

## 二分搜索模板

浮点数二分模版
```c++
bool check(double x) {/* ... */} // 检查x是否满足某种性质

double bsearch_3(double l, double r)
{
    const double eps = 1e-6;   // eps 表示精度，取决于题目对精度的要求
    while (r - l > eps)
    {
        double mid = (l + r) / 2;
        if (check(mid)) r = mid;
        else l = mid;
    }
    return l;
}
```

给一个**有序数组**和目标值，找第一次/最后一次/任何一次出现的索引，如果没有出现返回-1

模板四点要素

- 初始化：l=0、r=len-1
- 循环条件：
    - 搜索，查找，旋转数组中搜索 l <= r
    - 找数组第一个，最后一个数, 旋转数组中找最小值 l < r
- 比较中点和目标值：A[mid] ==、 <、> target
- 判断最后两个元素是否符合：A[l]、A[r] ? target

时间复杂度 O(logn)，使用场景一般是有序数组的查找

典型示例

[binary-search](https://leetcode-cn.com/problems/binary-search/)

> 给定一个  n  个元素有序的（升序）整型数组  nums 和一个目标值  target  ，写一个函数搜索  nums  中的 target，如果目标值存在返回下标，否则返回 -1。

```c++
// 整数二分搜索最常用模板
class Solution {
  public:
  int search(vector<int>& nums, int target) {
      int l = 0, r = nums.size() - 1;
      while (l <= r)
      {
          int mid = (l + r) >> 1;
          if (nums[mid] == target) return mid;
          else if (nums[mid] > target) r = mid - 1;
          else l = mid + 1;
      }
      return -1;
  }
};
```

大部分二分查找类的题目都可以用这个模板，然后做一点特殊逻辑即可

## 常见题目

### [search-for-range](https://www.lintcode.com/problem/search-for-a-range/description)

> 给定一个包含 n 个整数的排序数组，找出给定目标值 target 的起始和结束位置。
> 如果目标值不在数组中，则返回`[-1, -1]`

思路：核心点就是找第一个 target 的索引，和找第一个数位置和最后一个数二分位置的模版

```c++
class Solution {
public:
    int binary_search_first(vector<int>& nums, int target)
    {
        if (nums.size() == 0) return -1;
        int l = 0, r = nums.size() - 1;
        while (l < r)
        {
            int mid = l + r >> 1;
            if (nums[mid] >= target) r = mid; // r开始
            else l = mid + 1;
        }

        if (nums[l] != target) return -1;
        else return l;
    }

    int binary_search_last(vector<int>& nums, int target)
    {
        if (nums.size() == 0) return -1;
        int l = 0, r = nums.size() - 1;
        while (l < r)
        {
            int mid = l + r + 1 >> 1;
            if (nums[mid] <= target) l = mid;
            else r = mid - 1; // 这边减1 mid需要加1
        }

        if (nums[l] != target) return -1;
        else return l;
    }
    vector<int> searchRange(vector<int>& nums, int target) {

        vector<int> res;
        int res1 = binary_search_first(nums, target);
        int res2 = binary_search_last(nums, target);

        res.push_back(res1);
        res.push_back(res2);

        return res;
    }
};
```

### [search-insert-position](https://leetcode-cn.com/problems/search-insert-position/)

> 给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

```c++
class Solution {
public:
    // 在一个有序数组中找第一个大于等于target的下标
    int searchInsert(vector<int>& nums, int target) {
        int n = nums.size();
        int left = 0, right = n - 1, ans = n;
        while (left <= right) {
            int mid = ((right - left) >> 1) + left;
            if (target <= nums[mid]) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
};
```

### [search-a-2d-matrix](https://leetcode-cn.com/problems/search-a-2d-matrix/)

> 编写一个高效的算法来判断  m x n  矩阵中，是否存在一个目标值。该矩阵具有如下特性：
>
> - 每行中的整数从左到右按升序排列。
> - 每行的第一个整数大于前一行的最后一个整数。

```c++
class Solution {
public:
    // 将矩阵每一行拼接在上一行的末尾，会得到一个升序数组，可以在该数组上二分找到目标元素。
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();
        int l = 0, r = m * n - 1;
        while (l <= r) // 若特判了 等于target这种情况， 这边=
        {
            int mid = l + r >> 1;
            int x = matrix[mid / n][mid % n]; // 二维坐标映射
            if (x == target) return true;
            else if (x > target) r = mid - 1; // 特判过等于target, 所以这边 + 1
            else l = mid + 1;
        }

        return false;
    }
};
```

### [first-bad-version](https://leetcode-cn.com/problems/first-bad-version/)

> 假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。
> 你可以通过调用  bool isBadVersion(version)  接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。

```c++
class Solution {
public:
    int firstBadVersion(int n) {

        long l = 1, r = n; // 1 ~ n个版本
        while (l < r) // 循环条件 l < r
        {
            long mid = l + r >> 1;
            if (isBadVersion(mid)) r = mid;
            else l = mid + 1;
        }
        return l;
    }
};
```

### [find-minimum-in-rotated-sorted-array](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转( 例如，数组  [0,1,2,4,5,6,7] 可能变为  [4,5,6,7,0,1,2] )。
> 请找出其中最小的元素。

```c++
func findMin(nums []int) int {
class Solution {
public:
    // 旋转后会从中间分为上下两端序列
    // 右边端点的最大值 会小于左边端点的最小值
    // 没有重复情况
    int findMin(vector<int>& nums) {

        int l = 0, r = nums.size() - 1;
        while (l < r) // 循环条件 l < r
        {
            int mid = l + r >> 1;
            if (nums[mid] < nums[r]) r = mid; // 这里check函数为小于
            else l = mid + 1;
        }

        return nums[r];
    }
};
```

### [find-minimum-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转
> ( 例如，数组  [0,1,2,4,5,6,7] 可能变为  [4,5,6,7,0,1,2] )。
> 请找出其中最小的元素。(包含重复元素)

```c++
class Solution {
public:
    int findMin(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;

        while (l < r) // 循环条件 l < r
        {
            int mid = (l + r) >> 1;
            if (nums[mid] < nums[r]) r = mid;
            else if (nums[mid] > nums[r]) l = mid + 1;
            // 由于它们的值相同，所以无论 nums[r] 是不是最小值，都有一个它的「替代品」nums[mid]，因此我们可以忽略二分查找区间的右端点。
            else r -= 1;
        }

        return nums[r];
    }
};
```

### [search-in-rotated-sorted-array](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转。
> ( 例如，数组  [0,1,2,4,5,6,7]  可能变为  [4,5,6,7,0,1,2] )。
> 搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回  -1 。
> 你可以假设数组中不存在重复的元素。

```c++
class Solution {
public:
    // 旋转后的数组前后都是各自升序的，二分
    // 旋转数组前面序列高于后面序列
    // 不存在重复元素
    int search(vector<int>& nums, int target) {
        int l = 0, r = nums.size() - 1;
        while (l <= r)
        {
            int mid = (l + r) >> 1;
            if (nums[mid] == target) return mid;

            if (nums[0] <= nums[mid]) // 在左半端高的序列里
            {
                if (nums[0] <= target && target < nums[mid]) r = mid - 1; // 在左边分区
                else l = mid + 1;
            }
            else // 在右半边低的序列里
            {
                if (nums[mid] < target && target <= nums[nums.size() - 1]) l = mid + 1; // 在右半边分区
                else r = mid - 1;
            }
        }

        return -1;
    }
};
```

注意点

> 面试时，可以直接画图进行辅助说明，空讲很容易让大家都比较蒙圈

### [search-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转。
> ( 例如，数组  [0,0,1,2,2,5,6]  可能变为  [2,5,6,0,0,1,2] )。
> 编写一个函数来判断给定的目标值是否存在于数组中。若存在返回  true，否则返回  false。(包含重复元素)

```c++
class Solution {
public:
    // 旋转后的数组前后都是各自升序的，二分
    // 旋转数组前面序列高于后面序列
    // 存在重复元素, 所以碰到二分两边数值都相同时把区间缩小再继续二分
    bool search(vector<int>& nums, int target) {
        int n = nums.size();
        int l = 0, r = n - 1;
        while (l <= r) {
            int mid = (l + r) / 2;
            if (nums[mid] == target) {
                return true;
            }
            // 无法确认区间，缩小区间再继续二分
            if (nums[l] == nums[mid] && nums[mid] == nums[r]) {
                ++l;
                --r;
            // 在左半端高的序列里
            } else if (nums[l] <= nums[mid]) {
                if (nums[l] <= target && target < nums[mid]) {
                    r = mid - 1;
                } else {
                    l = mid + 1;
                }
            // 在右半边低的序列里
            } else {
                if (nums[mid] < target && target <= nums[n - 1]) {
                    l = mid + 1;
                } else {
                    r = mid - 1;
                }
            }
        }
        return false;
    }
};
```

## 练习题

- [ ] [search-for-range](https://www.lintcode.com/problem/search-for-a-range/description)
- [ ] [search-insert-position](https://leetcode-cn.com/problems/search-insert-position/)
- [ ] [search-a-2d-matrix](https://leetcode-cn.com/problems/search-a-2d-matrix/)
- [ ] [first-bad-version](https://leetcode-cn.com/problems/first-bad-version/)
- [ ] [find-minimum-in-rotated-sorted-array](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)
- [ ] [find-minimum-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)
- [ ] [search-in-rotated-sorted-array](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)
- [ ] [search-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)
