### 基数排序：[164. Maximum Gap](https://leetcode-cn.com/problems/maximum-gap/)

题思路：主要是排序算法满足$O(N)$时间复杂度的要求，只有基数排序和桶排序，这里主要复习一下基数排序的内容。

**基数排序**：

思路：将所有待比较数值（正整数）统一为同样的数位长度，数位较短的数前面补零。然后，从最低位开始，依次进行一次排序。这样从最低位排序一直到最高位排序完成以后，数列就变成一个有序序列。

基数排序的方式可以采用LSD（Least significant digital）或MSD（Most significant digital），LSD的排序方式由键值的最右边开始，而MSD则相反，由键值的最左边开始。这种排序方式要和数组更新方式，遍历数组的方式保持一致性。

基数排序一般要快过基于比较的排序，比如快速排序，但是也要看比较的位数和n的大小关系，而且基数排序适用于整数排序，应用场景比较受限。

Radix sort API:

```c++
vector<int> buffer(n);
int exp=1;
int maximum = *max_element(nums.begin(), nums.end());

while(exp <= maximum){
    vector<int> cnt(10, 0);
    for(int& num:nums){
        // for内的变量也是局部变量。
        int digit = (num/exp)%10;
        cnt[digit]++; 
    }
    // 关键是按基数的位置再次排数据。
    for(int i=1;i<10;i++){
        cnt[i]+=cnt[i-1];
    }
    for(int i=n-1;i>=0;i--){
        int digit = (nums[i]/exp)%10;
        // 放到当前digit对应的最后一个元素的位置，然后再--；
       	// 即先处理的大的元素，是放在最后面。
        buffer[cnt[digit]-1] = nums[i];
        cnt[digit]--;
    }
    copy(buffer.begin(), buffer.end(), nums.begin());
    exp*=10;
}
```

### 归并排序：[493. Reverse Pairs](https://leetcode-cn.com/problems/reverse-pairs/)

思路：归并排序的基本思路还是分治法，该题可以用分治法来处理。也可以说归并排序就是在分治的时候创建sorted数组，根据分治的结果双指针填满sorted数组，然后将sorted数组对原数组进行修改，使原数组满足有序性。

```c++
int reversePairsRecursive(vector<int>& nums, int left, int right) {
    if (left == right) {
        return 0;
    } else {
        int mid = (left + right) / 2;
        int n1 = reversePairsRecursive(nums, left, mid);
        int n2 = reversePairsRecursive(nums, mid + 1, right);
        int ret = n1 + n2;

        // 首先统计下标对的数量
        int i = left;
        int j = mid + 1;
        while (i <= mid) {
            while (j <= right && (long long)nums[i] > 2 * (long long)nums[j]) j++;
            ret += (j - mid - 1);
            i++;
        }

        // 随后合并两个排序数组
        vector<int> sorted(right - left + 1);
        int p1 = left, p2 = mid + 1;
        int p = 0;
        while (p1 <= mid || p2 <= right) {
            if (p1 > mid) {
                sorted[p++] = nums[p2++];
            } else if (p2 > right) {
                sorted[p++] = nums[p1++];
            } else {
                if (nums[p1] < nums[p2]) {
                    sorted[p++] = nums[p1++];
                } else {
                    sorted[p++] = nums[p2++];
                }
            }
        }
        for (int i = 0; i < sorted.size(); i++) {
            nums[left + i] = sorted[i];
        }
        return ret;
    }
}
```

### 贪心：[861. Score After Flipping Matrix](https://leetcode-cn.com/problems/score-after-flipping-matrix/)

思路：分析该问题的特征，确保前面的元素权重是恒大于后面元素的权重，就可以用贪心处理。进制问题一般都有前面的元素恒大于后面的所有元素的最利情况，所以可以用贪心处理。

### 回溯：[842. Split Array into Fibonacci Sequence](https://leetcode-cn.com/problems/split-array-into-fibonacci-sequence/)

思路：根据状态进行判定和枚举，标准的回溯法。在c++中，回溯法要记得留终结状态的数据，不然就是list.push_back之后list.pop_back，回溯完成就return; 什么都没有留下。

### 贪心：[621. Task Scheduler](https://leetcode-cn.com/problems/task-scheduler/)

思路：贪心，选择对当前任务而言，出现频次最高的任务，可以证明为最优解。

证明：

贪心的数学归纳反证法思路：（1）证明选择与当前贪心不同的策略，总target函数只会变成最优解及以下。（2）证明当前选择用贪心策略，可以至少得到比当前解更优的决策。

这里采用模拟的思路，当用模拟的思路时，每个元素都必须有一个状态，来表征元素和当前进程的关系，可以在合适的时候处理元素，比如该题有（nextValidTime, rest）来表示当前的状态。

### 前缀和+归并排序：[327. Count of Range Sum](https://leetcode-cn.com/problems/count-of-range-sum/)

思路：对于连续区间的加和满足要求的问题，一般都采用前缀和的思路，但是前缀和的时间复杂度是$O(n^2)$。

前缀和将连续区间转换为下标的问题，即元素之间的相对位置是不变的，最后结果就是不变的。

利用归并排序，子结构的相对位置关系，可以优化时间复杂度，对当前结构的归并排序而言，其子结构`left-mid, mid-right`都是已经排好序的，所以在操作子结构时需要进行假设，但是最后需要对有序子结构进行合并。

NOTE: 为了使前缀和`pre[j]-pre[i]=sum(arr[i]+...+arr[j-1])`满足要求，需要使pre[0]=0，即前缀和的一个元素是人为添加的0。

```c++
class Solution {
public:
    int countRangeSumRecursive(vector<long>& sum, int lower, int upper, int left, int right) {
        if (left == right) {
            return 0;
        } else {
            int mid = (left + right) / 2;
            int n1 = countRangeSumRecursive(sum, lower, upper, left, mid);
            int n2 = countRangeSumRecursive(sum, lower, upper, mid + 1, right);
            int ret = n1 + n2;

            // 首先统计下标对的数量
            int i = left;
            int l = mid + 1;
            int r = mid + 1;
            while (i <= mid) {
                while (l <= right && sum[l] - sum[i] < lower) l++;
                // r不满足要求
                while (r <= right && sum[r] - sum[i] <= upper) r++;
                ret += (r - l);
                i++;
            }

            // 随后合并两个排序数组
            vector<int> sorted(right - left + 1);
            int p1 = left, p2 = mid + 1;
            int p = 0;
            while (p1 <= mid || p2 <= right) {
                if (p1 > mid) {
                    sorted[p++] = sum[p2++];
                } else if (p2 > right) {
                    sorted[p++] = sum[p1++];
                } else {
                    if (sum[p1] < sum[p2]) {
                        sorted[p++] = sum[p1++];
                    } else {
                        sorted[p++] = sum[p2++];
                    }
                }
            }
            for (int i = 0; i < sorted.size(); i++) {
                sum[left + i] = sorted[i];
            }
            return ret;
        }
    }

    int countRangeSum(vector<int>& nums, int lower, int upper) {
        long s = 0;
        vector<long> sum{0};
        for(auto& v: nums) {
            s += v;
            sum.push_back(s);
        }
        return countRangeSumRecursive(sum, lower, upper, 0, sum.size() - 1);
    }
};
```

