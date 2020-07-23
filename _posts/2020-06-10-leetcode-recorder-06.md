---
layout: post
title:  "leetcode algorithm-06"
date:   2020-06-10
categories: study
tags: Leetcode
subtitle: Algorithm record:Heap, Union-Find
---

# 刷题记录-06-10

### 堆：[Top K Frequent Elements](https://leetcode-cn.com/problems/top-k-frequent-elements/)

思路：较为模板，求前 k 大，用小根堆，求前 k 小，用大根堆，大小特性是特定的。

```python
from heapq import heappush, heappop, heapreplace
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        d = collections.Counter(nums)
        res = []
        num = []
        for key,value in d.items():
            if len(res) < k:
                heappush(res,(value,key))
            elif value > res[0][0]:
                heapreplace(res,(value,key))
        for val,key in res:
            num.append(key)
        return num
```

### 堆：[Ugly Number II](https://leetcode-cn.com/problems/ugly-number-ii/),[Super Ugly Number](https://leetcode-cn.com/problems/super-ugly-number/)

思路：多指针+动态规划，也可以用堆，但是堆的空间是$O(n*k)$，相对来说比动态规划的空间要大一些。

### 并查集：[Most Stones Removed with Same Row or Column](https://leetcode-cn.com/problems/most-stones-removed-with-same-row-or-column/)

思路1：连通分量的概念，每次移出连通分量中石子数目-1的点。连通分量的话，使用DFS和Union-Find都是可以的。

java：

```java
class Solution {
    public int removeStones(int[][] stones) {
        int N = stones.length;
        int[][] graph = new int[N][N];
        // 也可以建立邻接表，其实都是一样的，遍历的方式不同而已。

        for(int i=0; i < N; i++){
            for(int j = 0; j < N; j++){
                if(stones[i][0]==stones[j][0] || stones[i][1] == stones[j][1]){
                    graph[i][j] = 1;
                }else{
                    graph[i][j] = 0;
                }
            }
        } 
        int res = 0;
        boolean[] visited = new boolean[N]; //default: false
        for(int i = 0; i < N; i++){
            if(visited[i] != true){
                // 重新建连通分支，且要减个一
                Stack<Integer> stack = new Stack();
                stack.push(i);
                res--;
                visited[i] = true;
                while(!stack.isEmpty()){
                    int node = stack.pop();
                    res++;
                    for(int j=0; j < N; j++){
                        if(graph[node][j]==1){
                            if(visited[j] != true){
                                visited[j] = true;
                                stack.push(j);
                            } 
                            }
                        }
                    }
                }
            }
        return res;
        }
   }
```

**思路2：并查集**

这里对并查集的概念梳理一下，做个总结。

概念：集合树、代表节点

步骤：初始化，将所有节点看作一个只包含自身的集合树，如果只包含自身，则元素已经是根节点（代表节点），那么`pre[i]==i`，之后通过Union-Find-Union...来形成一定数量的连通分支。

```python
class DSU:
    # 将p作为指针数组，指向代表节点。
    def __init__(self,N):
        self.p = list(range(N))

    def find(self, x):
        if self.p[x] != x:
            self.p[x] = self.find(self.p[x])
        return self.p[x]

    # 这里是不是还可以进行路径压缩？
    def union(self, x, y):
        xr = self.find(x)
        yr = self.find(y)
        self.p[xr] = yr

class Solution:
    def removeStones(self, stones: List[List[int]]) -> int:
    # 由于x和y必定是无关的两个变量，所以可以在有限x数量时，扩大空间做两个独立变量。
        N = len(stones)
        dsu = DSU(20000)
        for x,y in stones:
            dsu.union(x,y+10000)
        return N-len(set(dsu.find(x) for x,y in stones))
```

**路径压缩：**

路径压缩是将整个并查集形成的树扁平化，在实现过程中，在find函数里加上`parent[element] = parent[parent[element]]`，将当前节点直接指向自己父亲的父亲节点，减少深度。

```java
public int find(int element){
    while(element != parent[element]){
        parent[element] = parent[parent[element]];
       	element = parent[element;
    }
 	return element;
}
```

根据Algorithm书，最优化的解法是路径压缩+平衡，这里用python代码写一个Union-Find高效的写法：

```python
class UF:
    def __init__(self, n):
        # 初始化每个节点都为单独的连通分支,且树的深度初始化为1
        self.count = n
        self.parent = [i+1 for i in range(n)]
        self.rank = [1 for i in range(n)]
        
   	def get_count(self):
        return self.count
    
   	def find(self,x):
        while x != self.parent[x]:
            self.parent[x] = self.parent[self.parent[x]] 
            x = self.parent[x]
		return self.parent[x]
    
    def is_connected(self, x, y):
        return self.find(x) == self.find(y)
    
    def union(self, x, y):
        x1 = find(x)
        y1 = find(y)
        if (x1==y1):
            return
        if self.rank[x1] > self.rank[y1]:
            self.parent[x1] = y1
     	elif self.rank[x1] < self.rank[y1]:
            self.parent[x1] = y1
        else:
            self.parent[x1] = y1
         	self.rank[y1] += 1
       	self.count -= 1
```

### **并查集：[Longest Consecutive Sequence](https://leetcode-cn.com/problems/longest-consecutive-sequence/)**

C++代码参考[jarvis](https://leetcode-cn.com/problems/longest-consecutive-sequence/solution/ha-xi-biao-shi-xian-on-bing-cha-ji-liang-chong-shi/).

```c++
class Solution {
public:
    unordered_map<int,int> uf, cnt;

    int find(int i){
        return i == uf[i] ? i: uf[i] = find(uf[i]);
    }

    int merge(int x, int y){
        x = find(x); y = find(y);
        if(x == y) return cnt[x];
        uf[y] = x;
        cnt[x] += cnt[y];
        return cnt[x];
    }

    int longestConsecutive(vector<int>& nums) {
        if(nums.size() == 0) return 0;
        for(int i: nums) uf[i] = i, cnt[i] = 1;
        int ans = 1;
        for(int i: nums){
            if(i != INT_MAX && uf.count(i+1)) ans = max(ans, merge(i, i+1));
        }
        return ans;
    }
};
```

### DFS/BFS/回溯：[Word Ladder II](https://leetcode-cn.com/problems/word-ladder-ii/)

这个题的难度太高，后面的双向BFS+DFS我已经到了不想看（看不懂）的地步了。

参考：[windliang](https://leetcode-cn.com/problems/word-ladder-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-3-3/)

思路1：首先采用回溯的方法，onechange函数来判断关联性，通过长度来剪枝，通过endword==beginword来返回。这个方法中，可以用BFS建立图中邻接表的方法，省去了一层循环。

```java
class Solution {
    public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
        List<List<String>> ans = new ArrayList<>();
        ArrayList<String> temp = new ArrayList<String>();
        // ans结果，temp存储当前路径
        temp.add(beginWord);
        findLadderHelper(beginWord, endWord, wordList, temp, ans);
        return ans;
    }
    int min = Integer.MAX_VALUE;

    private void findLadderHelper(String beginWord, String endWord, List<String> wordList, ArrayList<String> temp, List<List<String>> ans){
        // 这里做了一个回溯函数，寻找可能的路径。
        if(beginWord.equals(endWord)){
            if(min > temp.size()){
                ans.clear();
                min = temp.size();
                ans.add(new ArrayList<String>(temp));
            }else if (min == temp.size()){
                ans.add(new ArrayList<String>(temp));
            }
            return;
        }
        // 剪枝
        if(temp.size() >= min){
            return;
        }

        for(int i = 0; i < wordList.size(); i++){
            String curr = wordList.get(i);
            if(temp.contains(curr)){
                continue;
            }
            if(onechange(beginWord, curr)){
                temp.add(curr);
                findLadderHelper(curr,endWord,wordList,temp,ans);
                temp.remove(temp.size()-1);
            }
        }
    }

    private boolean onechange(String beginWord, String curWord){
        int count = 0;
        for(int i = 0; i < beginWord.length(); i++){
            if(beginWord.charAt(i) != curWord.charAt(i)){
                count += 1;
            }
            if(count==2){
                return false;
            }
        }
        return count == 1; //是否只有一个不同，不可以相同，主要是与自身重合。
    }
}
```

思路2：回溯的方法会多出来比较多的无用路径，超时了，BFS可以直接判断最小路径。

```java
class Solution {
   public List<List<String>> findLadders(String beginWord, String endWord, List<String> wordList) {
        List<List<String>> res = new ArrayList<>();
        Set<String> distSet = new HashSet<>(wordList);
        if (!distSet.contains(endWord)) {
            return res;
        }

        Set<String> visited = new HashSet<>();
        Queue<List<String>> queue= new LinkedList<>();
        List<String> list = new ArrayList<>(Arrays.asList(beginWord));
        queue.add(list);
        visited.add(beginWord);

        boolean flag = false;
        while (!queue.isEmpty() && !flag) {
            int size = queue.size();
            Set<String> subVisited = new HashSet<>();
            for (int i = 0; i < size; i++) {
                List<String> path = queue.poll();
                String word = path.get(path.size() - 1);
                List<String> neighbor = neighbors(word,distSet);
                for(String str: neighbor){
                    if (!visited.contains(str)) {
                        List<String> pathList = new ArrayList<>(path);
                        pathList.add(str);
                        if (str.equals(endWord)) {
                            flag = true;
                            res.add(pathList);
                        }
                        queue.add(pathList); 
                        subVisited.add(str);
                    }
                }
            }
            visited.addAll(subVisited);
        }
        return res;    
   } 

    private List<String> neighbors(String cur, Set<String> dict){
        List<String> res = new ArrayList<>();
        char[] chars = cur.toCharArray();
        for(int i = 0; i < chars.length; i++){
            char temp = chars[i];
            for(char ch = 'a'; ch <= 'z'; ch++){
                if(ch==temp){
                    continue;
                }
                chars[i] = ch;
                // 这里为什么要赋新值？
                String n = new String(chars);
                if(dict.contains(n)){
                    res.add(n);
                }
            }
            chars[i] = temp;
        }
        return res;
    }
}
```



