---
layout: post
title:  "leetcode algorithm-03"
date:   2020-05-27
categories: study
tags: Leetcode
subtitle: Algorithm record:Topological Sorting, graph, state compression DP, BFS and DFS.
---



# 刷题记录-0527

### 拓扑排序：[课程表 II](https://leetcode-cn.com/problems/course-schedule-ii/)

思路：这里用到了贪心算法，对每个课程都有前馈课程，如果前馈课程都修完（数组非空），则可以把包含该课程的所有前馈课程列表都去除，如果所有课程都能学习，返回True。

之所以叫拓扑排序，**拓扑排序（Topological Sorting**）是一个有向无环图（DAG, Directed Acyclic Graph）的所有顶点的线性序列。满足以下规则:

1. 每个顶点出现且只出现一次。
2. 若存在一条从顶点 A 到顶点 B 的路径，那么在序列中顶点 A 出现在顶点 B 的前面。

做法也很模板：

1. 从 DAG 图中选择一个 没有前驱（即入度为0）的顶点并输出（贪心）。

2. 从图中删除该顶点和所有以它为起点的有向边。

3. 重复 1 和 2 直到当前的 DAG 图为空或当前图中不存在无前驱的顶点为止。后一种情况说明有向图中必然存在环。

python：

```python
class Solution(object):
    def findOrder(self, numCourses, prerequisites):
        # pre代表当前的课程入度
        pre = [0 for i in range(numCourses)]
        post = [[] for i in range(numCourses)]
        for i in range(len(prerequisites)):
            pre[prerequisites[i][0]] += 1
            post[prerequisites[i][1]].append(prerequisites[i][0])
        # 除了prequisite之外，还要建立与后面的数组的联系，形成有向图
        return self.bfs(pre,post,numCourses)

    def bfs(self,pre,post,numCourses):
        queue = []
        res = []
        for i in range(len(pre)):
            if pre[i] == 0:
                queue.append(i)
        while queue:
            # 度为0的课程
            temp = queue.pop(0)
            res.append(temp)
            for course in post[temp]:
                pre[course] -= 1
                if pre[course] == 0:
                    queue.append(course)
        if len(res) == numCourses:
            return res
        else:
            return []
```



### **状态压缩动态规划**：[每个元音包含偶数次的最长子字符串](https://leetcode-cn.com/problems/find-the-longest-substring-containing-vowels-in-even-counts/)

这个题的知识含量还是很多的，包括状态压缩，动态规划，位运算，前缀和和哈希。

**状态压缩**

前提：位运算：

`&,<<`：判断一个数字$x$二进制下第$i$位是否等于1，`if((1<<(i-1))&x)>0`

`|,<<`：将数字$x$二进制下的第$i$位更改成1，`x = x|(1<<(i-1))`

`&`：把数字二进制最靠右的第一个1去掉，`x=x&(x-1)`

`^`：异或操作，位相同，则为0，`0^x=x`

在状态压缩中，所有可能的状态集合用二进制来表示。

**思路：**在该题中，对每个元音的奇偶值都有一个状态，用二进制`11111`表示全奇数，一共有32种状态。如果`nums[0:i]`和`nums[0:j]`具有相同的状态，那么中间的`nums[i:j]`一定满足状态不变要求。在这道题中，状态的改变需要用到异或操作来改变奇偶。

```python
class Solution:
    def findTheLongestSubstring(self, s: str) -> int:
        D = {"a": 1, "e": 2, "i": 4, "o": 8, "u": 16}
        state = [float('inf') for i in range(32)]
        state[0] = -1 #为了满足长度的要求
        l = len(s)
        cur = 0
        res = 0
        for i in range(l):
           	cur ^= D.get(s[i],0)
      		if state[cur]==float('inf'):
                state[cur] = i
          	else:
                ans = max(ans,i-state[cur])
       	return ans
```

### KMP算法：[实现 strStr()](https://leetcode-cn.com/problems/implement-strstr/)

**1.暴力匹配思路**，这个是超时的。

- 如果当前字符串匹配成功，则继续匹配下一个字符
- 如果匹配失败，$i$回溯到初始位置的下一个位置，$j=0$，即$i = i-(j-1),j=0$.

**2.Knuth-Morris-Pratt(KMP) algorithm**

- 关于算法详解可以看这个博主的文章[KMP](https://blog.csdn.net/v_JULY_v/article/details/7041827)。
- 其中$next[j]==k$指不包括$p[j]$的模式串中最大长度为$k$的相同前缀与后缀，再次匹配需要让$j = next[j]$以避免完全暴力回溯，所以让模式串右移$j-next[j]$，从而使前面的部分继续匹配。
- 该方法的$next$数组指明了模式串在失配后应该跳到哪个位置。
- 在KMP算法中，重要的是求模式串$P$中的各个前缀后缀的最大公共元素表，之后就可以右移，初始值赋-1得到$next$数组，两个是一样的。前缀和后缀就是相当于字符串`z=xy`中x,y的组成，最大长度表也可以看该图。

![image-20200520214010445](\image\image-20200520214010445.png)



### 二分法：[寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

通用解法：二分求第K个最小值

**思路1：**我一开始的思路是传统二分，其实应该是对`K`值不断二分，然后K肯定能等于1，等于1的时候，存两个数，之后看奇偶取舍。

图来自[windliang](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/):

![1590310013(1)](\image\image-20200527159031001.jpg)

 python:

```python
    def findkmin(self, nums1, nums2, k):
        # 最好确保 nums1<nums2
        if len(nums1) > len(nums2):
            return self.findkmin(nums2,nums1,k)
        if len(nums1) == 0:
            return nums2[k-1]
        if k == 1:
            return min(nums1[0],nums2[0])
        # half 代表要各取多少个数
        half = k//2
        if half > len(nums1):
        # up 和 down 代表上下索引
            up = len(nums1) -1 
        else:
            up = half - 1
        down = half -1 
        if nums1[up] <= nums2[down]:
            return self.findkmin(nums1[up+1:],nums2,k-up-1)
        else:
            return self.findkmin(nums1, nums2[down+1:],k-half)
```

### 广度优先搜索：[N叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-n-ary-tree/)

这个题不难，这里只是把广度优先搜索梳理一下。

BFS算法是一种盲目搜索的算法，目的在于系统展开并检查图中的所有节点，以寻找可能的结果，它不考虑结果可能的地址，直到找到结果为止。

实现方法：

1. 首先将根节点放入队列中。
2. 从队列中取出第一个节点，并检验它是否为目标。
   - 如果找到目标，则结束搜索并回传结果。
   - 否则将它所有尚未检验过的直接子节点加入队列中，同时将根节点删除（这就是为什么要用队列）。
3. 若队列为空，表示整张图都检查过了——亦即图中没有欲搜索的目标。结束搜索并回传“找不到目标”。
4. 重复步骤2。

关于BFS和DFS：

一般情况下，是BFS+队列，DFS+栈，除了递归的方法之外，这两个办法实在太模板了...