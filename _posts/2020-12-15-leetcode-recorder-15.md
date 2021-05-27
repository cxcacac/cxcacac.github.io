这次刷题，主要是上个月打卡没有写的五道题，都一起写了，并理解了。

### 没写的

（树状数组写法）https://leetcode-cn.com/problems/reverse-pairs/solution/fan-zhuan-dui-by-leetcode-solution/

相似题：

从全局->局部，从全部->最优子结构动态规划

- [316. 去除重复字母](https://leetcode-cn.com/problems/remove-duplicate-letters/)（困难）
- [321. 拼接最大数](https://leetcode-cn.com/problems/create-maximum-number/)（困难）
- [402. 移掉 K 位数字](https://leetcode-cn.com/problems/remove-k-digits/)（中等）
- [1081. 不同字符的最小子序列](https://leetcode-cn.com/problems/smallest-subsequence-of-distinct-characters/)（中等）

数据结构：

线段树+树状数组。

#### [327. Count of Range Sum](https://leetcode-cn.com/problems/count-of-range-sum/)

#### [493. Reverse Pairs](https://leetcode-cn.com/problems/reverse-pairs/)

c++语法不会

#### [49. Group Anagrams](https://leetcode-cn.com/problems/group-anagrams/)

### 动态规划：[376. Wiggle Subsequence](https://leetcode-cn.com/problems/wiggle-subsequence/)

思路：对于该问题而言，关键要找到合适的状态进行表示，如果要满足$O(n)$的时间复杂度，需要以前i个序列的解作为状态，然后进行循环即可，不可以包含最后一个元素的状态进行求解。

如果要以前i个序列作为合适的状态，需要确定最优子结构的转换关系。

设置两个dp数组为up, down，分别表示该序列为上升子序列的解，或者该序列为下降子序列的解，**然后根据nums[i]与nums[i-1]的关系判断上升子序列的更新和下降子序列的更新。**

### 哈希表和函数关系：[290. Word Pattern](https://leetcode-cn.com/problems/word-pattern/)

思路：一层哈希表其实就是一层函数关系，比如实现$x\rightarrow y$的映射关系，其实就是建立$hash(x) \rightarrow y$，然后如果是双射(bijection)，即一一对应关系，就是建立两个哈希表，即$hash(x)\rightarrow y, hash(y)\rightarrow x$。

在条件句中，如果建立联系就是：如果当前集合包含该元素，就必须对应另外一个元素（两个判断），否则就直接建立双向联系。

### 单调栈：[316. Remove Duplicate Letters](https://leetcode-cn.com/problems/remove-duplicate-letters/)

思路：单调栈就是维护相对顺序保持不变的升序或者降序序列，符合该题的预期。对于去除重复字符的字符串而言，如果要使字典序最小小，最重要的是保证其字符是按照升序排列的。

### 并查集：[803. Bricks Falling When Hit](https://leetcode-cn.com/problems/bricks-falling-when-hit/)

思路：打砖块或者打气球，气球从有到无的变化可能难以建立联系，但是有时候从逆向的角度，气球从无到有的话，带来的变化可能是可以做的。

### 数组：[1232. Check If It Is a Straight Line](https://leetcode-cn.com/problems/check-if-it-is-a-straight-line/)

思路：对该数组的第一个元素和第零个元素而言，是恒满足条件的，可以通过赋初值来解决问题，不需要那么多的假设条件。

