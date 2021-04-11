### 排序+贪心：[406. Queue Reconstruction by Height](https://leetcode-cn.com/problems/queue-reconstruction-by-height/)

思路：对于有序数列，可能会存在放入前后的问题。因此进行排序后，可以得到有序数列，对有序数列判断简单操作是否具有全局最优解，若有，就是贪心。

tip：有时候遍历一遍比map更管用，反而用map需要各种条件，限制住了手脚。

### 桶排序：[1030. Matrix Cells in Distance Order](https://leetcode-cn.com/problems/matrix-cells-in-distance-order/)

思路：可以用自定义排序，也可以使用桶排序，因为桶排序的时间复杂度要低。

桶排序的思想就是你需要先明确这些元素最大值最小值的个数，一旦确定，之后再进行分桶。

```c++
class Solution {
public:
    int dist(int r1, int c1, int r2, int c2) {
        return abs(r1 - r2) + abs(c1 - c2);
    }

    vector<vector<int>> allCellsDistOrder(int R, int C, int r0, int c0) {
        int maxDist = max(r0, R - 1 - r0) + max(c0, C - 1 - c0);
        vector<vector<vector<int>>> bucket(maxDist + 1);

        for (int i = 0; i < R; i++) {
            for (int j = 0; j < C; j++) {
                int d = dist(i, j, r0, c0);
                vector<int> tmp = {i, j};
                bucket[d].push_back(move(tmp));
            }
        }
        
        vector<vector<int>> ret;
        for (int i = 0; i <= maxDist; i++) {
            for (auto &it : bucket[i]) {
                ret.push_back(it);
            }
        }
        return ret;
    }
};
```

### 数组的双指针-快慢性质：[283. Move Zeroes](https://leetcode-cn.com/problems/move-zeroes/)

思路：虽然是简单题，但是用双指针的话，能反映出来数组处理的一些奇妙之处。

可以采用`i,j`双指针，其中`j`是用来遍历的，`i`是用来记录第一个0的位置的。

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int i=0, j=0;
        while(j<nums.size()){
            if(nums[j]){
                swap(nums[j], nums[i++]);
            }
            j++;
        }
    }
};
```

这里面有点动态规划的思想，因为`j`是快指针，能保证它处理过的元素没有零，因为初始条件决定的。

这里面的指针有两个性质：

- 左指针左边均为非零数；
- 右指针左边直到左指针处均为零。

### 排序+贪心：[452. Minimum Number of Arrows to Burst Balloons](https://leetcode-cn.com/problems/minimum-number-of-arrows-to-burst-balloons/)

思路：排序贪心，最重要的就是确定贪心想法，之后根据元素排序确定处理先后关系（这与从前向后处理或从后向前处理有关），从而验证贪心的至少最优解正确性。

和其他合并区间类的题目套路一样, 都是贪心思想, 先排序, 然后遍历检查是否满足合并区间的条件。

比如该题，贪心就是对单个气球而言，尽可能把箭设置在右边，从而能够让其尽可能照顾到所有元素，并可以通过数学归纳与反证法验证贪心关系。

### 完全二叉树+位运算：[222. Count Complete Tree Nodes](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

思路：对于最大层数为h的完全二叉树，节点个数在$[2^h, 2^{h+1}-1]$之间，可以通过二分查找的方式得到完全二叉树的节点个数。

完全二叉树和二进制也有关系，如果第K个节点位于第h层（root节点在第0层），那么k的二进制包括h+1位，最左边的元素为10000....，之后依次加一。

因此对二进制而言，0可以表示上一个根节点转到了左节点，1可以表示为上一个根节点转到了右节点。因此该节点与上一节点的关系就是$val_{root}<<1 + 0/1$.

### 分治+哈希：[454. 4Sum II](https://leetcode-cn.com/problems/4sum-ii/)

思路：这里有四组，分开处理，其他的和Two sum的思路是一样的，主要是分治的思想来减少时间复杂度，如果没有分治好，就是$O(n^2-n^3)$的时间复杂度。

教训：不要记题，记题会让你的思路僵化，一开始的路就走错了。

