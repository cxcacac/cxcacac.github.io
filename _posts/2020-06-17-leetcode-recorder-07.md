---
layout: post
title:  "leetcode algorithm-07"
date:   2020-06-17
categories: study
tags: Leetcode
subtitle: Algorithm record:Union-Find, binary-Search
---


# 刷题记录-06-17

### 并查集：[Accounts Merge](https://leetcode-cn.com/problems/accounts-merge/)

思路：并查集的作用之一，建立不同元素之间的联系，即建立连通分支，这里是建立不同邮箱账号之间的联系。

用的工具比较多，也说明设计的一个点，可以做功能模块，但是不先做功能。

### 并查集：[Smallest String With Swaps](https://leetcode-cn.com/problems/smallest-string-with-swaps/)

思路：如果相邻字母能转无数次，其实相当于做一个排序，这也是排序的基本流程。所以可以建立连通分支后排序。

这里采用[发狂的桔子](https://leetcode-cn.com/u/fa-kuang-de-jie-zi/)的思路，里面确定连通分支的内容可以作为一个固定的方法，使用map和defaultdict(list)，但要注意，不能直接采用下面的代码模块，因为不能确保每个数值都指向根节点，find函数可以改parent节点的位置，但是并不能完全让元素指向根节点。

```python
        for key,val in d.items():
            link[val].append(key)
```

最终代码：

```python
class Solution:
    def smallestStringWithSwaps(self, s: str, pairs: List[List[int]]) -> str:
        # 如果能转无数次，说明能任意换位置，建立连通分支
        # 对每个连通分支排序，之后填入即可。
        d = {i:i for i in range(len(s))}
        def find(x):
            while d[x] != x:
                d[x] = d[d[x]]
                x = d[x]
            return x

        for pair in pairs:
            x = find(pair[0])
            y = find(pair[1])
            if x < y:
                d[y] = x
            else:
                d[x] = y
        
        link = collections.defaultdict(list)
        # 确定连通分支内元素所在的坐标，利用根节点的key值和list存储
        for key,val in enumerate(map(find, d.keys())):
            link[val].append(key)

        arr = list(s)
        # 给出连通分支的内容
        for l in link.values():
            t = sorted(arr[i] for i in l)
            for idx,val in zip(sorted(l),t):
                arr[idx] = val
        return ''.join(arr)  
```

### 分治算法：[Different Ways to Add Parentheses](https://leetcode-cn.com/problems/different-ways-to-add-parentheses/)

思路：分治即是分为两个部分调用函数，不一定是二分的位置，从$0.....（n-1）$都有可能，快速排序用的就是这个思路。在这个问题中，对两个部分的划分是根据运算符的位置确定的，然后对分好的部分调用递归函数即可，要注意两点：（1）递归终止条件（2）递归返回值的类型。

下面的思路来自于：[江不知](https://leetcode-cn.com/u/jalan/)

```python
class Solution:
    def diffWaysToCompute(self, input: str) -> List[int]:
        # 分治法的分而治之并不是二分，而是寻找到子结构。
        if input.isdigit():
            return [int(input)]
        
        res = []
        for i in range(len(input)):
            if input[i] in ['*', '+', '-']:
                left = self.diffWaysToCompute(input[:i])
                right = self.diffWaysToCompute(input[i+1:]) 
                for m in left:
                    for n in right:
                        if input[i] == '+':
                            res.append(m+n)
                        if input[i] == '*':
                            res.append(m*n)
                        if input[i] == '-':
                            res.append(m-n)
        return res 
```

加了备忘录后：

```python
class Solution:
    def diffWaysToCompute(self, input: str) -> List[int]:
        # 分治法的分而治之并不是二分，而是寻找到子结构。
        # defaultdict最大的作用是默认值。
        m = {}
        if input.isdigit():
            m[input] = [int(input)]
            return [int(input)]

        res = []
        for i in range(len(input)):
            if input[i] in ['*', '+', '-']:
                lstr = input[:i]
                rstr = input[i+1:]
                if lstr in m:
                    left = m[lstr]
                else:
                    left = self.diffWaysToCompute(lstr)
                    m[lstr] = left
                if rstr in m:
                    right = m[rstr]
                else:
                    right = self.diffWaysToCompute(rstr)
                    m[rstr] = right
           
                for j in left:
                    for k in right:
                        if input[i] == '+':
                            res.append(j+k)
                        if input[i] == '*':
                            res.append(j*k)
                        if input[i] == '-':
                            res.append(j-k)
        return res 
```

还有[windliang的解答](https://leetcode-cn.com/problems/different-ways-to-add-parentheses/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-5-5/)，思路很流畅，从递归到memoization，再到动态规划。

动态规划：

### 二分法：[Find Peak Element](https://leetcode-cn.com/problems/find-peak-element/)

思路：二分法就是按照一定规律成倍缩小查找区间的方法，不一定是大小的要求。

这里面根据nums[i]和nums[i+1]的关系缩小区间（左右边界）

