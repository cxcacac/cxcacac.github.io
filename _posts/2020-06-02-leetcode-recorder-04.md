---
layout: post
title:  "leetcode algorithm-04"
date:   2020-06-02
categories: study
tags: Leetcode
subtitle: Algorithm record:Bit manipulation, BackTracking, Graph.
---

# LeetCode刷题笔记-0603

### 回溯算法（Backtracking）

**题目：[字母大小写全排列](https://leetcode-cn.com/problems/letter-case-permutation/)**

该问题是比较典型的全排列问题。

通用解法的步骤有点类似于BFS，都是保留一个可执行的选择列表（队列），但是**回溯算法需要在递归调用之前做选择，在递归调用之后撤销选择。**这也是回溯问题的精髓所在，**用递归剪枝的思路解决枚举方法**。（剪枝就是知道问题是错的，马上退出来，不往下接着走。）

**全排列问题**

```java
List<List<Integer>> res = new LinkedList<>();

List<List<Integer>> permute(int[] nums) {
    LinkedList<Integer> track = new LinkedList<>();
    backtrack(nums, track);
    return res;
}

void backtrack(int[] nums, LinkedList<Integer> track) {
    // 结束条件
    if (track.size() == nums.length) {
        res.add(new LinkedList(track));
        return;
    }

    for (int i = 0; i < nums.length; i++) {
        // 排除不合法的选择
        if (track.contains(nums[i]))
            continue;
        // 做这一层的决策选择
        track.add(nums[i]);
        // 进入下一层决策树
        backtrack(nums, track);
        // 取消选择，来获取在所有可能的选择列表中平行的决策。
        track.removeLast();
    }
}
```

### 前缀和

**题目：[和可被 K 整除的子数组](https://leetcode-cn.com/problems/subarray-sums-divisible-by-k/)**

思路：该题用到前缀和的思想，因为被K整除可以采用同余的思想，即利用hashmap进行判定，如果存在，则添加。

前缀和其实就是一个数学表达式：
$$
preSum[i] = A[0]+A[1]+...+	A[i]\\
preSum[j]-preSum[i] = A[i+1]+....+A[j]
$$
前缀和用到了转化的想法，因为题目要求是连续数组，就换一种方式表示连续数组，通过前缀和表示连续数组的任意元素和加和，前缀和和差分数组是相同的，都是申请diff(n+1)和pre(n+1)的数组。

### **矩阵**

**题目：[N皇后](https://leetcode-cn.com/problems/n-queens/)**

思路：典型的回溯法，关键在矩阵元素的处理上。

矩阵最大的性质还是它的有序性，其中一条对对角线元素，在和主对角线平行的元素上，`x-y=const`，主对角线上的元素最小，两边对称。在次对角线上的所有元素，`x+y=const`，次对角线上的元素最大，之后两边对称。

### 位运算

**题目：[只出现一次的数字 II](https://leetcode-cn.com/problems/single-number-ii/)**

题意：有数字只出现１次，其他的都出现３次，求只有１次那个。

普通的异或运算：`1^0 = 1, 1^1 = 0`，这里每一位只有两个状态，因此同一个数作用也只有两个状态。

对于位运算，可以统计每位出现的次数，然后对3取余判断是否为1，最后判定num|=total<<i;

**题目：[颠倒二进制位](https://leetcode-cn.com/problems/reverse-bits/)**

位运算翻转，即得到对应位，然后转换到对称的位置，即0转到31位。

python：

```python
class Solution:
    def reverseBits(self, n: int) -> int:
        res = 0
        for i in range(32):
            res = res|((n>>(32-i-1))&1)<<i
        return res
```

### 图+BFS/DFS

题目：**[克隆图](https://leetcode-cn.com/problems/clone-graph/)**

思路：图的遍历，不涉及到路径问题，就用传统的DFS和BFS;

其中DFS需要vis的标记，**dfs的函数目的是将结点copy并返回copy后的结点。**

BFS需要vis的标记，没有标记过的结点copy后，将copy结点加queue，之后进行遍历。

dfs：

```python
class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        visited = {}
        # 这个visited是对原图的处理进行记录，字典存储的是图中节点在深拷贝图中对应的节点，一种map关系。
        def dfs(node):
            if node == None:
                return 
            if node in visited:
                return visited[node]
            new = Node(node.val)
            visited[node] = new
            # 然后对深拷贝的节点进行修改。
            for n in node.neighbors:
                new.neighbors.append(dfs(n))
            return new
        return dfs(node)
```

bfs：

```python
class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        visited = {}
        def bfs(node):
            if not node: return
            new = Node(node.val)
            visited[node] = new  
            queue = [node]
            while queue:
                temp = queue.pop(0)
                for n in temp.neighbors:
                    if n not in visited:
                        visited[n] = Node(n.val)
                        queue.append(n)
                    visited[temp].neighbors.append(visited[n])
            return new # 返回root节点。
        return bfs(node)
```
