---
layout: post
title:  "leetcode algorithm-04"
date:   2020-06-02
categories: study
tags: Leetcode
subtitle: Algorithm record:Bit manipulation, BackTracking, Graph.
---

# LeetCode刷题笔记-0603

### 回溯算法（Backtracking）：[字母大小写全排列](https://leetcode-cn.com/problems/letter-case-permutation/)

注：算法的理解一部分来自于[labuladong](https://labuladong.gitbook.io/algo/di-ling-zhang-bi-du-xi-lie/hui-su-suan-fa-xiang-jie-xiu-ding-ban).

通用解法的步骤有点类似于BFS，都是保留一个可执行的选择列表（队列），但是**回溯算法在递归调用之前做选择，在递归调用之后撤销选择。**

**全排列问题**

高中时候的排列组合，其实就是决策树的模型，固定前缀即固定决策树的前半深度，最后叶节点的个数就是全排列的个数。

在回溯问题中，`1,2,3,4`的排列，`[1,2]`就是路径，这是已经做好的选择，后面的3,4就是可选择元素，结束条件就是可选择元素为空的时候。

至于选择和撤销选择，在决策树模型中也很常见。

不过有的时候术语也挺烦人。什么叫剪枝？就是知道问题是错的，马上退出来，不往下接着走。

这里直接看一下labuladong的java代码，递归相对来说比较好理解，（函数功能，终止条件，子状态）这三个弄清楚就可以。

```java
List<List<Integer>> res = new LinkedList<>();

/* 主函数，输入一组不重复的数字，返回它们的全排列 */
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

总结：

回溯算法，本质上还是暴力算法，只不过解决了连续状态的求解问题，和动态规划是有点相似的，只不过不是重叠子问题。

回溯算法，可以用决策树来解释。

- 同一深度的决策单元是平行的，所以需要用循环来遍历。

- 在循环执行过程中，对当前的排列状态进行修改是为了往下一层深度进行递归
- 处理下一个平行结点，需要恢复修改前状态，再移动、修改、递归、添加，最后返回。
- 也可以不删除，不过要记录一下修改之前的状态，比如说下标，值等等，这里面一定要清楚修改状态。

虽然算法很模板，但是因为是暴力解法，通用性很强，所以需要具体问题具体分析，常见于组合排列问题。

### 前缀和：[和可被 K 整除的子数组](https://leetcode-cn.com/problems/subarray-sums-divisible-by-k/)

前缀和其实就是一个数学表达式：
$$
preSum[i] = A[0]+A[1]+...+	A[i]\\
preSum[j]-preSum[i] = A[i+1]+....+A[j]\\
$$
前缀和用到了转化的想法，因为题目要求是连续数组，就换一种方式表示连续数组，通过前缀和表示连续数组的任意元素和加和。

有点像离散化的积分和导数。

### **矩阵**：[N皇后](https://leetcode-cn.com/problems/n-queens/)

这个题，与其说是回溯法，不如说是矩阵处理算法，这个答案是借鉴评论里一个高票的答案的，主要是对矩阵的处理有所认识，虽然也很简单，认识后就是小学数学。

矩阵最大的性质还是它的有序性，其中一条对对角线元素，在和主对角线平行的元素上，`x-y=const`，主对角线上的元素最小，两边对称。在次对角线上的所有元素，`x+y=const`，次对角线上的元素最大，之后两边对称。

### 位运算：[只出现一次的数字 II](https://leetcode-cn.com/problems/single-number-ii/)

普通的异或运算：`1^0 = 1, 1^1 = 0`，这里每一位只有两个状态，因此同一个数作用也只有两个状态。

这个题需要记录三次，那么需要三个状态，即原来的每个二进制位，再用两个二进制位表示状态。

其中用到按位取反运算符`~`，还用到了数电中的状态转换表，得到`one = one ^ n & ~two`,`two = two ^ n & ~one`，因此用one，two两个变量代表状态。

### 位运算：[颠倒二进制位](https://leetcode-cn.com/problems/reverse-bits/)

几个位操作的进阶版：

`(n>>i)&1`：为了看n的第i+1位。

`res|num`：如果res和num不存在进位的问题，就是两者的加和。

`num&(num-1)`：把num的最后一位变成0，也可以说是将二进制中消去一个1.

在C/C++中，0x表示十六进制的前缀字符，0是八位的前缀字符，十进制没有前缀字符。

十六进制中的特殊字符：

`0xaaaaaaaa = 10101010101010101010101010101010 (偶数位为1，奇数位为0）`

`0x55555555 = 1010101010101010101010101010101 (偶数位为0，奇数位为1）`

`0x33333333 = 110011001100110011001100110011 (1和0每隔两位交替出现)`

`0xcccccccc = 11001100110011001100110011001100 (0和1每隔两位交替出现)`

`0x0f0f0f0f = 00001111000011110000111100001111 (1和0每隔四位交替出现)`

`0xf0f0f0f0 = 11110000111100001111000011110000 (0和1每隔四位交替出现)`

`0xffffffff`。

计算机中存储数值，都是**以二进制补码的形式存入的**，**正数的补码是他本身，负数的补码是反码加一**。

原码-反码-补码（-0）

**原码：**10000000000000000000000000000000 （第一位为1，后面31个0）

**反码**：11111111111111111111111111111111 （32个1）

**补码（反码+1）**：00000000000000000000000000000000 （32个0）

因此数字原码表示的范围为11111111111111111111111111111111

反码为1000000000000000000000000000000000，+1为补码。

python：

```python
class Solution:
    def reverseBits(self, n: int) -> int:
        res = 0
        for i in range(32):
            res = res|((n>>(32-i-1))&1)<<i
        return res
```

注：解位操作可解的题，不能单纯的背公式，要分析每一位上的`1或0`是随着位操作是如何变化的，有没有一定的规律性。

### 图+BFS/DFS：[克隆图](https://leetcode-cn.com/problems/clone-graph/)

图遍历最典型的题。

这里直接引用[powcai](https://leetcode-cn.com/problems/clone-graph/solution/dfs-he-bfs-by-powcai/)大佬的解释了。

其中dfs：

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
                    # 把原图的每一个neighbors节点都拷贝到该节点的neighbors节点中，这里面已经有一部分建立了联系。
            return new # 返回root节点。
        return bfs(node)
```

### 深度优先遍历DFS+图：[课程表](https://leetcode-cn.com/problems/course-schedule/)

判断一个图中是否有环，可以用深度优先搜索的方式。

设置一个状态量，如果在深度遍历过程中，发现状态量重合，则说明有环，否则将所有邻接节点遍历完，恢复状态变量。

