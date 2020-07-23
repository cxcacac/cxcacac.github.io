---
layout: post
title:  "leetcode algorithm-08"
date:   2020-06-24
categories: study
tags: Leetcode
subtitle: Algorithm record:Two pointers, Dynamic Programming, Bit calculation.
---

# 刷题记录-06-24

### 双指针：[Remove Duplicates from Sorted Array II](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array-ii/)

思路：利用双指针，需要判断一下该元素是否与两个位置之前的数组元素是否相等，如果不相等，则需要赋到对应的指针所在的位置。代码非常精妙。

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        # sorted order.
        # if two number are arranged, can neglect the element by comparisions.
        # two pointers
        i = 0
        for num in nums:
            if i < 2 or nums[i-2] < num:
                nums[i] = num
                i += 1 
        return i
```

### 动态规划：[Regular Expression Matching](https://leetcode-cn.com/problems/regular-expression-matching/)

思路：二维数组的状态转移方程：$dp[i][j] = f(dp[i-1][j-1],dp[i-1][j],dp[i][j-1])$，只不过在这个题中考虑的状态比较复杂和多一点而已。

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        m = len(s)
        n = len(p)
        dp = [[False for i in range(n+1)] for j in range(m+1)]
        dp[0][0] = True
        for j in range(2, n+1):
            dp[0][j] = dp[0][j-2] and p[j-1] == '*'
        for i in range(1,m+1):
            for j in range(1,n+1):
                # 两字符递进匹配，dp[i-1][j-1],  dp[i-1][j], dp[i][j-1]
                dp[i][j] = ((dp[i-1][j-1] and (p[j-1] == '.' or 
                s[i-1] == p[j-1])) or (dp[i][j-1] and p[j-1] == '*') or 
                (dp[i-1][j] and p[j-1] =='*' and ((i > 1 and s[i-1] == p[j-2]) or 
                p[j-2] == '.')) or (j > 1 and (dp[i][j-2] and p[j-1] == '*'))) 
        return dp[-1][-1]
```

### **字符串匹配：**[ Pattern Matching LCCI](https://leetcode-cn.com/problems/pattern-matching-lcci/)

这个做法，整个思路流程非常的通畅，从特殊情况简单处理，到复杂情况复杂处理。

C++:

```c++
class Solution {
public:
    int cnt[2];
    bool patternMatching(string pattern, string value) {
        // From simple to complex
        // consider one of the Strings equals to null.
        // merge all the possibilities to get a simpler form.
        // note the special
        if(pattern.empty()) return value.empty();
        if(value.empty()){
            int i = 0;
            while(i < pattern.size() && pattern[i] == pattern[0]) i++;
            return i == pattern.size();
        }
        int n = pattern.size(), m = value.size();
        cnt[0] = cnt[1] = 0;
        for(auto x: pattern) cnt[x-'a']++;
        // if(helper(value,cnt[0]) || helper(value, cnt[1])) return true;  
        // 上面这个代码如果统一的话，没有截止等于0的情况，后面取余数会有问题。
        if(!cnt[0]) return helper(value, cnt[1]);
        else if (!cnt[1]) return helper(value, cnt[0]);
        if (helper(value, cnt[1])) return true;
        if (helper(value, cnt[0])) return true;
        // In the following steps, a and b can not equals to null.
        for(int lena = 1; lena*cnt[0] <= m - cnt[1]; lena++){
            if((m-lena*cnt[0])%cnt[1] != 0) continue;
            int lenb = (m-lena*cnt[0])/cnt[1];
            if(check(pattern, value, lena, lenb)) return true;
        }
        return false;
    }
    bool helper(string value, int k){
        // 判断是否能切分k次，且全部相等。
        int m = value.size();
        if(m%k != 0) return false;
        int len = m/k;
        for(int i = len; i < m; i+=len){
            if(value.substr(i,len) != value.substr(0,len)) return false;
        }
        return true;
    }
    bool check(string pattern, string value, int lena, int lenb){
        string ps[2] = {"",""}; // double quotations
        // c++好像字符串不能直接相等。
        // j是该模式起始的位置。
        for(int i = 0, j = 0; i < pattern.size(); i++){
            if(pattern[i] == 'a'){
                // 如果没有被赋值，首先赋值，之后再进行比较。
                if(ps[0] == "") ps[0] = value.substr(j, lena);
                else if (value.substr(j,lena) != ps[0]) return false;
                j += lena;
            }
            else{
                if(ps[1] == "") ps[1] = value.substr(j, lenb);
                else if (value.substr(j, lenb) != ps[1]) return false;
                j += lenb;
            }
        }
        return true;
    }
};
```

### 动态规划/递归转换：[Unique Binary Search Trees II](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

思路：递归比较容易，只需要考虑主问题和次问题之间是怎么连接的，从上到下考虑即可。动态规划需要从下到上，看最优的子状态是如何形成最终的状态的，注重于状态之间的转换关系。一般情况下两者都是可以转换的。只要建立好子结构就可以。

```python
class Solution:
    def generateTrees(self, n: int) -> List[TreeNode]:
        # recursion，main section -> smaller section with same function.
        # dynamic pogramming -> relations between consecutive states, several states -> one state.
        # This is a binary search tree.
        # method 1, recursion.
        def helper(s, e):
            # e is exclusive, s is inclusive 
            # return all the possibilities under the root
            res = []
            if s >= e:
                return [None]
            for i in range(s,e):
                left = helper(s, i)
                right = helper(i+1, e)
                for l in left:
                    for r in right:
                        temp = TreeNode(i)
                        temp.left = l 
                        temp.right = r
                        res.append(temp) 
            return res 
        if n == 0:
            return None
        return helper(1,n+1)
```

### 位运算：[Add Binary](https://leetcode-cn.com/problems/add-binary/)

思路：主要是对carry和加和的处理，carry加到num数上，相当于移位之后多做一层num累加。

```python
class Solution:
    def addBinary(self, a, b) -> str:
        x, y = int(a, 2), int(b, 2)
        while y:
            # 直到carry没有为止，carry相当于进行移位累加。
            answer = x ^ y
            carry = (x & y) << 1
            x, y = answer, carry
        return bin(x)[2:]
    	# 需要把0b去掉。
```

### 动态规划：[Counting Bits](https://leetcode-cn.com/problems/counting-bits/)

思路：在动态规划中，可以分类建立子状态的关系，这道题就是把奇数和偶数分开，奇数和偶数之间的差别，奇数之间差1，偶数之间差1，建立联系即可。

所以关键就是这里并非是连续的状态转换关系，而是差了个2.

```python
class Solution:
    def countBits(self, num: int) -> List[int]:
        # 1,10,11,100,101....
        dp = [0 for i in range(num+1)]
        dp[0] = 0
        # 找到奇偶之间的关系，奇数比之前的偶数+1，偶数比之前的num>>1的数一样的。
        # 关系可以是分开创建的，即奇数和奇数建立联系，偶数和偶数建立联系。
        for i in range(1,num+1):
            if i%2 == 0:
                dp[i] = dp[i>>1]
            else:
                dp[i] = dp[i>>1] + 1
        return dp
```

