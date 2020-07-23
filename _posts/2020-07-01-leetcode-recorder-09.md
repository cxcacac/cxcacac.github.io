---
layout: post
title:  "leetcode algorithm-09"
date:   2020-07-01
categories: study
tags: Leetcode
subtitle: Algorithm record:Dynamic Programming
---

# 刷题记录-0701

### 博弈游戏+动态规划：[Stone Game](https://leetcode-cn.com/problems/stone-game/)

思路：是在二维 dp 的基础上使用元组分别存储两个人的博弈结果。动态规划的思路，其实本质上就是强化学习的思路，就是属于当前状态+选择过程。

tips：一般情况下，动态规划都可以写成递归，所以可以先用递归的方式想问题，找到状态之间的转移关系。

```python
class Solution:
    def stoneGame(self, piles: List[int]) -> bool:
        # 如果每次每个人都做出最优的选择，Alex还是赢，说明Alex must win the game
        # 因此在该问题中，每个人的策略都是相同的，可以用一个dp数组表示。
        n = len(piles)
        dp = [[piles[i] if i == j else 0 for i in range(n)] for j in range(n)]
        # 根据状态转移关系，为了满足从最小子状态建立的流程，需要从base的情况考虑dp数组如何遍历。
        # 在下面的遍历中，i表示包含的元素个数-1，j表示起始位置，dp[s][e]中s,e表示开始的位置和最后的位置。
        for i in range(1,n):
            for j in range(n-i):
                dp[j][j+i] = max(piles[j] - dp[j+1][j+i], piles[j+i] - dp[j][j+i-1])
        return dp[0][n-1] > 0                
```

### 数组元素和index：[First Missing Positive](https://leetcode-cn.com/problems/first-missing-positive/)

```python
class Solution:
    def firstMissingPositive(self, nums: List[int]) -> int:
        l = len(nums)
        for i in range(l):
            # 数组的值用做index来节省时间和空间。
            # 要注意利用被覆盖的值，所以这里是需要swap位置的。
            # 如果要swap的话，必须要用while循环来利用当前swap到的元素，否则还是没有利用到被覆盖的值。
            # 为了避免无限循环，有两个限制条件:
            # (1)当元素没法利用的时候需要停止. 
            # (2)当元素正好的定位到当前位置，即元素和它要对应的元素位置正好是相等的。
            while 0 < nums[i] <= l and nums[i] != nums[nums[i]-1]:
                temp = nums[i]
                nums[i], nums[temp-1] = nums[temp-1], nums[i]
        for key, val in enumerate(nums):
            if key + 1 != val:
                return key + 1
        return l + 1
```

### 动态规划：[Longest Valid Parentheses](https://leetcode-cn.com/problems/longest-valid-parentheses/)

思路：dp[i]表示以当前字符为结束的有效字符串长度(注意不是有效括号个数)。

其中这个问题有个隐藏的约束：即有效字符串的内部必须全部都是有效的括号，不可以单单出来一个字符。

**Summary about State：**

还有一类问题，状态设置是以当前字符或者当前下标作为边界的状态，在这里是有效字符串的长度。

差别就是一个必须包含最后的字符作为结束字符，一个不需要包含最后的字符。

上面两种不同的状态设置方式,主要有两个差别:

- 最后做O(n)的max(dp)的运算,或者直接输出dp[-1]

- 以当前index作为边界的状态设置少了一个必须包含当前元素的约束，需要考虑多种情况.

```python
class Solution:
    def longestValidParentheses(self, s: str) -> int:
        # 有效括号字符串,内部必须全部都是有效括号字符串.
        dp = [0 for i in range(len(s))]
        res = 0
        for i in range(1,len(s)):
            if s[i] == ')':
                pre = i - dp[i-1] - 1
                if pre >= 0 and s[pre] == '(':
                    dp[i] = dp[i-1] + 2
                    if pre > 0:
                        dp[i] += dp[pre-1]
            res = max(res,dp[i])
        return res 
```

### 动态规划：[Count Numbers with Unique Digits](https://leetcode-cn.com/problems/count-numbers-with-unique-digits/)

思路：这里的状态选择的位数，即dp[i]代表i位数的所有情况。

有了状态的选择之后，就可以状态之间的转移方程：`dp[i] = dp[i-1] + (dp[i-1]-dp[i-2])*(10-(i-1))`

其中，`dp[i-1]-dp[i-2]`是只考虑位数为`i-1`的情况，然后和最后一位的个数相乘，是一种排列组合。

由于`i`只依赖于`i-1`和`i-2`，所以这里可以采用节省内存的dp。

NOTE: 第一位为0不贡献位数，仍然是`i-1`

**Summary：**

动态规划其实就两个步骤：

- 状态的选择，`dp[i]`代表什么，`dp[i][j]...`代表什么状态，要有代表性，可以设置多个状态数组。
- 状态的转移，不同状态之间的关系是什么，或者不同状态数组之间的转移是什么

```python
class Solution:
    def countNumbersWithUniqueDigits(self, n: int) -> int:
        if n == 0:
            return 1
        if n == 1:
            return 10
        a = 1
        b = 10
        # b - a 是一种推导结果，只考虑i-1位的情况，最后一位的情况要看现在总共有多少位数。
        # 0不贡献位数。
        for i in range(2,n+1):
            c = b + (b-a)*(10-(i-1))
            a = b
            b = c
        return c
```

### 动态规划：[Wildcard Matching](https://leetcode-cn.com/problems/wildcard-matching/)

**summary：**动态规划的状态延续性，在某种程度上是一种承接，后一个状态可以由前一个状态修改后获得，也可以承接前一个状态，在这道题中，承接性做的好，省去了很多麻烦。

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        # classic dynamic programming
        # is it possible to have consecutive symbol "**"??
        m = len(s)
        n = len(p)
        dp = [[False for i in range(n+1)] for j in range(m+1)]
        dp[0][0] = True
        
        for j in range(1,n+1):
            if p[j-1] == "*":
                dp[0][j] = dp[0][j-1]
        
        for i in range(1,m+1):
            for j in range(1,n+1):
                if (s[i-1] == p[j-1]) or p[j-1] == "?":
                    dp[i][j] = dp[i-1][j-1]
                elif p[j-1] == '*':
                    dp[i][j] = dp[i-1][j] or dp[i][j-1] # empty sequence
    
        return dp[-1][-1]
```



