# N数之和

原题：[1两数之和](https://leetcode-cn.com/problems/two-sum)，[15三数之和](https://leetcode-cn.com/problems/3sum)，[18四数之和](https://leetcode-cn.com/problems/4sum)，[16较接近的三数之和](https://leetcode-cn.com/problems/3sum-closest)

# 两数之和

两数之和就不解释了：

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        std::map<int, int> map;
        std::vector<int> ans;
        int len = nums.size();
        for (int i = 0; i < len; i++) {
            int other = target - nums[i];
            auto it = map.find(other);
            if (it == map.end()) {
                map.insert(pair<int, int>(nums[i], i));
            }
            else {
                ans.push_back((*it).second);
                ans.push_back(i);
                return ans;
            }
        }
        return ans;
    }
};
```

# 三数之和

两数之和是一个O(N)的算法。那么三数之和呢？

可以简单地想：当确定第一个数之后，后两个数的和也就随之确定。于是，我们可以遍历所有可能的第一个数，然后将问题转化成两数之和问题。因此，我们需要求解N个两数之和问题，时间复杂度为O(N^2)。

这样就结束了吗？并没有。因为注意原题中提到：**答案中不可以包含重复的三元组**。而上面的方法，是无法有效地去除重复三元组的。尽管我们可以用哈希表来做到这一点，不过并不优雅。

优雅的方法是什么呢？是双指针法。具体而言，我们首先将数组排序得到一个有序数组，然后

- 枚举所有可能的第一个数
- 在第一个数后面的子数组中，使用双指针查找

那么怎么去重呢？由于在这种方法中，我们得到的解一定是有序的，也就意味着：如果两个答案的第一个数不同，则两个答案就是不同的；如果第二个数或第三个数不同，则答案也是不同的。所以，我们只需要在上述两个步骤中，分别跳过重复的值，就可以得到正确答案。

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        size_t length = nums.size();

        std::sort(nums.begin(), nums.end());

        for(int i = 0; i < length; i++) {
            if(i > 0 && nums[i] == nums[i - 1]) continue;
            int target = -nums[i];
            int high = length - 1, low = i + 1;
            while(low < high) {
                int sum = nums[high] + nums[low];
                if(sum == target) {
                    result.push_back({nums[i], nums[high], nums[low]});
                    while(low < high && nums[high] == nums[high - 1]) high--;
                    high--;
                    while(low < high && nums[low] == nums[low + 1]) low++;
                    low++;
                } else if(sum < target) {
                    while(low < high && nums[low] + nums[high] < target) low++;
                } else {
                    while(low < high && nums[low] + nums[high] > target) high--;
                }
            }
        }
        return result;
    }
};
```

# 四数之和

有了三数之和的铺垫，四数之和就就很简单了。略过。

# 最接近的三数之和

其实和三数之和是一样的，不过做出一点小改变：遍历每种情况时，都计算一次当前的和，并与最优解进行比较。

如果你愿意再优化，可以让每次内部的`while`循环运行结束之后，再计算和并比较，这样就避免计算重复的和。不过意义似乎不大。

# 引申

三数之和目前已知的最好复杂度为：

$$O(n^2(loglogn)^{O(1)}/log^2n)$$

不过你真的需要学会它吗？