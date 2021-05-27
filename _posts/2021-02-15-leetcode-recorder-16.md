### 差分数组：[995. Minimum Number of K Consecutive Bit Flips](https://leetcode-cn.com/problems/minimum-number-of-k-consecutive-bit-flips/)

### 单调队列：[1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit](https://leetcode-cn.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/)

### 限制条件+穷举：[395. Longest Substring with At Least K Repeating Characters](https://leetcode-cn.com/problems/longest-substring-with-at-least-k-repeating-characters/)

思路：将问题加一个限制条件，转换成能够采用滑动窗口解决的问题，然后将该限制条件进行穷举。

例如：限制每个substring只能最多有n种lower case character，外面加一层循环，将最外层的条件穷举即可。

### 动态规划+位运算：[338. Counting Bits](https://leetcode-cn.com/problems/counting-bits/)

思路：动态规划，重要的是状态表示和转移关系，在转移关系中，除了+1，-1，+2，-2等，还有关于index的位运算，即num[i>>1]和num的关系如何。

### 数组：[1658. Minimum Operations to Reduce X to Zero](https://leetcode-cn.com/problems/minimum-operations-to-reduce-x-to-zero/)

思路：trick，如果只从数组两端遍历数据，相当于做连续数组，可以用滑动窗口来解题。

### 字符串算法：[1392. Longest Happy Prefix](https://leetcode-cn.com/problems/longest-happy-prefix/)

思路：Robin-Karp算法