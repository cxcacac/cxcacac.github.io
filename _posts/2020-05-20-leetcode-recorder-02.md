---
layout: post
title:  "leetcode algorithm-02"
date:   2020-05-20
categories: study
tags: Leetcode
subtitle: Algorithm record:ListNode, Tree, Recursion, Iteration
---


# 刷题记录-0520

### **链表题：[K个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)**

**思路1：**通过快慢指针的思路，将链表用$start$和$end$指针截出来一段，将这一段进行$reverseList()$，为了保持位置不变性，需要记住要反转链表的前一个元素的位置$pre$和后一个元素的位置$next$，翻转完成后更新指针$start,end,pre,next$，而且这四个节点对应的位置是有关系的。

代码：

```python
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        dummy = ListNode(0)
        dummy.next = head
        # 创造两个指针，同时指向dummy的位置，pre表示链表前面的元素位置，end是用来指向翻转链表的最后一个元素。
        pre = dummy
        end = dummy
        while end.next != None:
            for i in range(k):
                if end != None:
                    end = end.next
                else:
                    break
            if end == None:
                break
            start = pre.next 
            Next = end.next 
            end.next = None
            # 反转链表后进入下一段，此时start的位置已经变到最后一位了，首先将start.next的位置和Next连上。
            pre.next = self.reverse(start)
            start.next = Next
            pre = start
            end = pre
        return dummy.next

    def reverse(self, head):
        pre = ListNode(0)
        curr = head
        while curr != None:
            Next = curr.next 
            curr.next = pre
            pre = curr
            curr = Next 
            # 链表之间的赋值会把next指向保留吗？
        return pre
```

注：赋值操作$l_1=l_2$，理解成$l_1$指针指向$l2$。

反转单向链表：

```java
public Node reverseList(Node head){
    // 需要两个指针
    // pre指向已反转链表的头节点
    // next指向未反转链表的头节点
    Node pre = null;
    Node next = null;
    while (head != null) {
		// 保存当前头节点的下一个节点。
        next = head.next;
		// 将当前头节点的下一个节点指向最先前的节点pre，实现反转。
        head.next = pre;
        // 更新反转链表之后，也要更新pre的指向到已反转链表的头节点。
        pre = head;
		// head继续指向未反转链表的头节点进行下一次循环，直到head==null，反转完成。
        head = next;
    }
    return pre;
}
```

示意图：

注：图来自博主[yinhuanxu](https://blog.csdn.net/xyh269)，[参考链接1](https://blog.csdn.net/xyh269/article/details/70238501)。

- 第一层循环

![image-20200516161644040](.\image\image-20200516161644040.png)

- 第二层循环

![image-20200516161904990](.\image\image-20200516161904990.png)

**思路2：**使用栈，将一定长度的链表存入栈中，之后从栈顶弹出即可。从这里也可以看出栈和数组的相似性，只不过作为存储的两种形式，一种是$index$访问，量化存储。一种是多了一个$ListNode.next$建立联系，便于进行插入和删除操作。

代码：

```python
def reverseKGroup(self, head: ListNode, k: int) -> ListNode:   
	    dummy = ListNode(0)
        dummy.next = head

        p = dummy
        # Next 指针指向未翻转链表第一个位置
        Next = p.next
        while Next != None:
            stack = []
            count = k
            while count > 0 and Next != None:
                stack.append(Next)
                count -= 1
                Next = Next.next
            # 这个地方不能直接输出，因为k=1的情况，会导致没有输出。
            if count != 0:
                break
            # 将栈内的元素组成链表,p逐步指到到链表最后的一个元素。
            while stack:
                p.next = stack.pop()
                p = p.next 
            p.next = Next 
        return dummy.next 
```

**思路3：**递归，将链表分为两个部分，手动翻转+递归翻转的两个部分，如果最后没凑够k个整数，那么就不调用翻转了，这个递归应该是承接了翻转链表的一部分，即把$pre=cur$.

代码参考[powcai](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)：

```python
def reverseKGroup(self, head: ListNode, k: int) -> ListNode: 
    	# 引入curr指针
        cur = head
        count = 0
        while cur and count!= k:
            cur = cur.next
            count += 1
        if count == k:
            cur = self.reverseKGroup(cur, k)
            while count:
                tmp = head.next
                head.next = cur
                cur = head
                head = tmp
                count -= 1
            head = cur   
        return head
```

### 贪心算法题:[买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

有点类似于启发算法，引入成本的概念很有意思，待补充数学证明。

代码：

```python
def maxProfit(self, prices: List[int], fee: int) -> int:    
    	if len(prices) < 2:
            return 0
        profit = 0
        buy = sold = prices[0]
        for price in prices[1:]:
            if price >= sold:
                sold = price
            else:
                # 评估
                gain = sold - buy - fee
                # 第一个条件，保证赚钱，第二个条件，经济学概念，机会成本，赚的钱必须能够弥补机会成本才是赚的。
                if gain > 0 and gain > price - buy:
                    profit += gain
                    buy = sold = price
                # 如果出现最低价，则尽量抛售现在的股票，给未来更多机会。
                elif price < buy:
                    buy = sold = price
        # 时间到了，卖了赚不赚钱。
        if sold - buy > fee:
            profit += sold - buy - fee
        return profit
```

**思路2**：动态规划

代码：

```python
def maxProfit(self, prices: List[int], fee: int) -> int:    
	    cash = 0
        hold = -prices[0]
        # 可以看做持有股票是亏损的状态，如何让亏损最小化，就是买。
        for i in range(1,len(prices)):
            cash = max(cash,hold+prices[i]-fee)
            hold = max(hold,cash-prices[i])
        return cash
```

### 递归+树：[平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)

**思路1：**普通递归，自顶向下

代码比较简单。

```python
class Solution:
	def isBalanced(self, root: TreeNode) -> bool:
        if root == None:
            return True
        return abs(self.height(root.left)-self.height(root.right))<=1 and self.isBalanced(root.left) and self.isBalanced(root.right)

	def height(self, root):
        # 该函数是从该点为root节点的最大height
        if root == None:
            return 0
        return max(self.height(root.left)+1,self.height(root.right)+1)
```

**思路2：**剪枝递归，只要是不平衡子树，就$False$。从底至顶返回子树最大高度，若判定某子树不是平衡树则 “剪枝” ，直接返回。

```python
class Solution:
    def isBalanced(self, root: TreeNode) -> bool:
        return self.recur(root) != -1

    def recur(self, root):
        if root == None: return 0
        left = self.recur(root.left)
        if left == -1: return -1
        right = self.recur(root.right)
        if right == -1: return -1
        return max(left, right) + 1 if abs(left - right) < 2 else -1
```

### 递归+树：[将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

**思路1：**树是自上而下建立的，对平衡搜索二叉树而言，做中序遍历即可得到升序数组，根节点为数组中点，即$mid = (right+left)//2, root = TreeNode(nums[mid])$。由于二叉搜索树的左右子树也为搜索树，分治递归即可。

tips: 

对递归函数而言，最重要的就是明白当前函数应用于目标对象的目的是为了求什么，之后是否能够应用到与该目标对象有关系的，更小的目标对象。

### 递归+树：[二叉树的所有路径](https://leetcode-cn.com/problems/binary-tree-paths/)

**思路：**递归

tips：

​    （1）这道题反映，需要照顾递归函数的返回形式，这样也能作用于函数设计本身。

​    （2）其实递归函数就是作用于主结构->子结构->终止条件，返回值从终止条件->子结构->主结构。

### **迭代+树：**[二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

**思路1：**递归，比较常规和简单。

**思路2：**栈+迭代。

前序遍历迭代算法的伪代码：根->左->右

```python
stack
p = root
while p!= None or stack != None:
    while p != None:
        p.val
       	p.right -> stack
        p = p.left
    p = stack.pop()
return result about p.val
```

后序遍历迭代算法的伪代码：和上面类同，reverse（根->右->左）的结果。

中序遍历的迭代算法伪代码：

```python
stack 
p = root
while p!= None or stack != None:
    while p != None:
        p -> stack 
        p = p.left
    p = stack.pop()
    p.val
    p = p.right
return result
```

### 拓扑排序：[课程表 II](https://leetcode-cn.com/problems/course-schedule-ii/)

**思路1：**课程的关系是一个有向图，用到图的算法我还不是很熟悉，所以这里只进行算法的解释，参考[liweiwei](https://leetcode-cn.com/problems/course-schedule-ii/solution/tuo-bu-pai-xu-shen-du-you-xian-bian-li-python-dai-/)。

首先是图的表示，这里采用邻近节点表示，即以该树为$ancestor$的所有$sucessors$所组成的集合。

除了邻接表之外，当前课程是否可修由当前的$prerequisite$的课程个数是否为0，用一个$in\_degree$数组表示，这里我一开始想的是prequisite做一个集合，其实作为条件判断，不用明确$prerequisite$是哪个，只需要知道个数就可以，这就是简化。

python代码：

```python
class Solution:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
         # 课程的长度
        clen = len(prerequisites)
        if clen == 0:
            # 没有课程，当然可以完成课程的学习
            return [i for i in range(numCourses)]
        # 入度数组，一开始全部为 0

        in_degrees = [0 for _ in range(numCourses)]
        # 邻接表
        adj = [set() for _ in range(numCourses)]
        for course, pre in prerequisites:
            in_degrees[course] += 1
            adj[pre].add(course)
    
        # 首先遍历一遍，把所有入度为 0 的结点加入队列
        res = []
        queue = []
        for i in range(numCourses):
            if in_degrees[i] == 0:
                queue.append(i)    
        while queue:
            top = queue.pop(0)
            # 只有所有先修课程都已满足，才会把course加入到res中。
            res.append(top)
            for successor in adj[top]:
                in_degrees[successor] -= 1
                if in_degrees[successor] == 0:
                    queue.append(successor)

        if len(res) != numCourses:
            return []
        return res
```

**思路2：**深度优先搜索（Depth-First-Search），通过递归来判断有向图中是否有环，对当前的节点设置状态变量，如果在遍历的过程中发现该状态变量相等，即说明遍历到了同一个节点，否则没有遍历到同一个节点，对所有节点都采用这个递归过程。

注：（访问限制）在类中，对属性的名称前加“__”，可以让内部属性不被外部访问，成为一个私有变量或者私有函数，无法在外界直接对类的信息进行修改，必须要调用类的内部函数，或者可以用"\_classname\_\_attributename"来直接访问。

python:

```python
class Solution(object):

    def findOrder(self, numCourses, prerequisites):
        # 课程的长度
        clen = len(prerequisites)
        if clen == 0:
            return [i for i in range(numCourses)]
        # 逆邻接表
        inverse_adj = [set() for _ in range(numCourses)]
        for second, first in prerequisites:
            inverse_adj[second].add(first)

        visited = [0 for _ in range(numCourses)]
        res = []
        for i in range(numCourses):
            if self.__dfs(i,inverse_adj, visited, res):
                return []
        return res

    def __dfs(self, vertex, inverse_adj, visited, res):
        """
        :param visited: 记录了结点是否被访问过，2 表示当前正在 DFS 这个结点
        """
        # 2 表示这个结点正在访问，如果状态变量相同，则返回有环。
        if visited[vertex] == 2:
            return True
        if visited[vertex] == 1:
            return False
        # 设置状态变量
        visited[vertex] = 2
        # 递归访问前驱结点
        for precursor in inverse_adj[vertex]:
            if self.__dfs(precursor, inverse_adj, visited, res):
                return True
        # 还原状态变量。
        visited[vertex] = 1
        # 深度优先遍历递归的目的，先把 vertex 这个结点的所有前驱结点都输出之后，再输出自己
        res.append(vertex)
        return False
```

### 动态规划：[乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

**思路：**两个考虑（1）如果$nums[i]<0$，那么当前位置乘积最大值依赖于之前位置的乘积最小值，反之亦然。如果$nums[i]>0$那么当前位置乘积最大值依赖之前位置最大值，反之亦然。（2）当前的乘积是要延续之前的状态还是要重新选取。可以写出状态转移方程，同时记录最大值。

$$imax = max(imax,imax*nums[i]),imin = min(imin,imin*nums[i])。$$

python：

```python
class Solution:
    def maxProduct(self, nums: List[int]) -> int:
        imin = imax = res = nums[0]
        # 之所以能优化，是因为只依赖前面的值。
        for i in range(1,len(nums)):
            if nums[i]<0:
                imax,imin = imin,imax
            imax = max(nums[i],imax*nums[i])
            imin = min(nums[i],imin*nums[i])
            res = max(imax,res)
        return res 
```

注：github pages中支持latex语法,header中添加下面的代码，编辑的时候要写成latex语法。

```html
<!-- 数学公式 -->
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
      inlineMath: [['$','$']]
    }
  });
</script>

```

