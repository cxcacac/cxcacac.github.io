---
layout: post
title:  "leetcode algorithm-03"
date:   2020-05-27
categories: study
tags: Leetcode
subtitle: Algorithm record:Topological Sorting, graph, state compression DP, BFS and DFS.
---


# 刷题记录-0527

### 拓扑排序

**题1：[课程表 II](https://leetcode-cn.com/problems/course-schedule-ii/)**

思路：这里用到了贪心算法，对每个课程都有前馈课程，如果前馈课程都修完（数组非空），则可以把包含该课程的所有前馈课程列表都去除，如果所有课程都能学习，返回True。

**拓扑排序（Topological Sorting**）是一个有向无环图（DAG, Directed Acyclic Graph）的所有顶点的线性序列。满足以下规则:

- 每个顶点出现且只出现一次。
- 若存在一条从顶点 A 到顶点 B 的路径，那么在序列中顶点 A 出现在顶点 B 的前面。

做法也很模板：

- 建一个一维入度数组，表示每个结点的入度。建一个邻接二维数组，表示每个结点指向的所有结点。

- 从 DAG 图中选择一个没有前驱（即入度为0）的顶点并输出（贪心）。
- 从图中删除该顶点和所有以它为起点的有向边。
- 重复 1 和 2 直到当前的 DAG 图为空或当前图中不存在无前驱的顶点为止。后一种情况说明有向图中必然存在环。

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

### **状态压缩**

**题目：[每个元音包含偶数次的最长子字符串](https://leetcode-cn.com/problems/find-the-longest-substring-containing-vowels-in-even-counts/)**

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

### KMP算法

关于算法详解可以看这个博主的文章[KMP](https://blog.csdn.net/v_JULY_v/article/details/7041827)，具体实现见[github](https://github.com/cxcacac/Algorithms-implementation/blob/master/cpp_version/string%20algorithm/kmp.cpp)。

问题的出发点即是：假设一个文本串S，一个模式串P，要查找P在S中的位置，应该怎么查找？

- 首先是暴力匹配，双指针的思路，匹配失败则需要回退i，时间复杂度为O(mn)。
- KMP算法，也是采用双指针，不过采用了next数组来表明模式串在失配之后指针j应该跳到哪个位置，即j=next[j]，而i的位置是不回退的。

从直觉的角度，之所以存在next[j]，是因为next[j]位置之前的**前缀**之一（prefix, p[0]...p[k]）和p[j]之前的后缀之一是相同的，所以跳过前缀的部分，并保持指针i和next[j]继续比较。

**KMP核心部分：**

- 寻找前缀后缀最长公共元素长度（最重要）

  **前缀：prefix, p[0]...p[k]； 后缀：suffix, p[j]...p[n-1];** 而最大公共元素长度既有前缀属性，也有后缀属性。

  对index=j而言，其最长公共元素长度为0....j范围内的最大值，即从index=0遍历。

- 求next数组

  next[j]对应的元素不在前缀，即除去当前字符外的最长相同前缀后缀长度，所以将第一步的值向右移，初值为-1；

  next[j]对应着j失配后，要匹配的位置。

- 根据next数组进行匹配

**最大公共元素长度表和next数组**

两者是等价的，最大公共元素即包含了当前元素，而next数组不包含当前元素，表示失配转移位置，在具体处理上，将长度表值右移，首位=-1即可。

根据next[0].....next[j]，可以求出next[j+1]的值。

- 若next[j]=k，且p[j]==p[k]，则next[j+1] = next[j]+1 = k+1;
- 若p[j] != p[k]，那么需要看一下p[m]....p[j]是否有前缀串进行对应。回到next[k]数组，即0....k-1的前缀最大长度，如果p[k] == p[j]，那么next[j+1]= next[k] + 1; 否则继续递归调用。

代码的角度：

```c++
void GetNext(char* p, int next[]){
    int len = strlen(p);
    next[0] = -1;
    int k = -1;
    int j = 0;
    while(j<len-1){
       if(k == -1 || p[j] == p[k]){
           k++;
           j++;
           // next[j+1] = k+1;
           next[j] = k;
       }
       else{
           k = next[k];
       }
    }
}
```

**基于next匹配**

- 相配或者j=next[j]=-1，即s[i]=\=p[j] ||j== -1 ，则i++, j++;
- 不相配，即s[i] != p[j] ，j = next[j];

**next数组优化**

对于next数组而言，next[j]对应的index，即下一个与s[i]匹配的p[index]，若p[next[j]]==p[j]，下一步肯定会失配，继续p[next[index]]与s[i]匹配，因此可以对next进行优化，提前避免再次失配的情况。

从实践角度，即p[j]==p[k]，更新next[j+1]=k+1时，需要考虑失配元素p[j+1]是否和最大前缀对应的元素p[k+1]相同。不相同则维持next[j+1] = k+1，相同则next[j+1] = next[k+1]，next[k]先前遍历过，能确保避免再次失配。

```c++
void GetNext(char* p, int next[]){
    int len = strlen(p);
    next[0] = -1;
    int k = -1;
    int j = 0;
    while(j<len){
       if(k == -1 || p[j] == p[k]){
           k++;
           j++;
           // next[j+1] = k+1;
           if(p[j]==p[k]){
               next[j] = next[k];
           }else{
               next[j] = k;
           }
       }
       else{
           k = next[k];
       }
    }
}
```

**时间复杂度**

KMP的时间复杂度为O(m+n)，匹配过程时间复杂度是O(m+n)，i++右移，常数时间判断。


### 二分法

**题：[寻找两个正序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)**

通用解法：二分求第K个最小值

**思路1：**应该是对`K`值不断二分，然后K肯定能等于1，等于1的时候，存两个数，之后看奇偶取舍。

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

### 树(BFS)

**题：[N叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-n-ary-tree/)**

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

