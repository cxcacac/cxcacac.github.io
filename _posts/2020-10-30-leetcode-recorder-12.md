### 原地快速排序

快速排序如果每次都申请数组，用递归来做，是很简单的。找一个pivot，then compare, allocate to an array.

reference:https://magiclen.org/hackerrank-quicksort-in-place/

```java

```

### python函数：map,zip,all,any

[Check If a String Can Break Another String](https://leetcode-cn.com/problems/check-if-a-string-can-break-another-string/)

```python
class Solution:
    def checkIfCanBreak(self, s1: str, s2: str) -> bool:
        s1, s2 = sorted(s1), sorted(s2)
        check = zip(*((a >= b, a <= b) for a, b in zip(s1, s2)))
        return any(map(all, check))  
```

判断是否全为正，或者全为负。

### 模拟插入区间：[Insert Interval](https://leetcode-cn.com/problems/insert-interval/)

思路：用left和right表示插入后区间的变化，用left，right和区间的左右边界的关系判断是否overlap.

对两个区间而言，overlap则意味着除了left>interval[1], right<interval[0]的情况之外，根据overlap的情况不断更新left和right的值。

是一个流动的模拟。

### dfs和bfs建图处理字符：[Word Ladder](https://leetcode-cn.com/problems/word-ladder/)

思路：对于string接龙问题，如果只有一个字符的改变，可以创造虚拟节点，从而实现穷举的目的。

通过建图表示字符串之间的联系，将所有的字符串中间加入虚拟节点"string1+*+string2"。

使用BFS遍历图，其中对于已经遍历过的节点，直接用distant[word]进行判断，这在dfs遍历中也很常见，不用再做visited数组进行判断。

建立节点和关系：

```c++
void addWord(string& word) {
    if (!wordId.count(word)) {
        wordId[word] = nodeNum++;
        edge.emplace_back();
    }
}
void addEdge(string& word) {
    addWord(word);
    int id1 = wordId[word];
    for (char& it : word) {
        char tmp = it;
        it = '*';
        addWord(word);
        int id2 = wordId[word];
        edge[id1].push_back(id2);
        edge[id2].push_back(id1);
        it = tmp;
    }
}
```

最后由于完整的distance中，有axbxcxd的形式，所以最后结果res=distance/2+1;

### 自定义sort函数/count bits（java/c++）：[Sort Integers by The Number of 1 Bits](https://leetcode-cn.com/problems/sort-integers-by-the-number-of-1-bits/)

思路：该题的思路并不难，关键在于一些小的点怎么用。

count bits api的两个角度.

直接cnt;

```c++
cnt = 0;
while(x){
    cnt+=x&1;
    x>>=1;
}
// 函数也可以写成：
while(x){
    cnt+=(x%2);
    x/=2;
}
```

采用递推：bit[i]=bit[i>>1]+(i&1)，在该递推中x的范围应该有所限制。

```c++
vector<int> bit(10001, 0);
for(int i=0;i<10001;i++){
    bit[i]=bit[i>>1]+(i&1);
}
```

还有一个点就是关于java和c++自定义排序的问题，主要关注其中的匿名函数写法。

先看普通函数的写法，关于升序排序。c++

```c++
bool cmp(int a, int b){
    int numa=count(a), numb=count(b);
    return numa!=numb? numa<numb: a<b;
}
sort(arr.begin(), arr.end(), cmp);
return arr;
```

匿名函数：

```c++
vector<int> bit(10001, 0)
for(auto x: arr){
	bit[x]=count(x);
}
sort(arr.begin(), arr.end(), [&](int x,int y){
	if(bit[x]<bit[y]) return true;
    if(bit[x]>bit[y]) return false;
    return x<y;
});
return arr;
```

java

```java
int[] bit= new int[10001];
List<int> list = new ArrayList<>();
for(int x: arr){
    list.add(x);
    bit[x]=count(x);
}
// collections.sort对应的对象是list.
Collections.sort(list, new Comparator<Integer>(){
    public int compare(Integer x, Integer y){
        // 前面的小，后面的大。
        return bit[x]!=bit[y]? bit[x]-bit[y]:x-y;
    }
})
```

匿名函数：

```java
Arrays.sort(nums, (o1, o2)->{
    a = count(o1);
    b = count(o2);
    return a==b? o1-o2: a-b;
})
```

### 数组处理：[31. Next Permutation](https://leetcode-cn.com/problems/next-permutation/)

思路：如果对一个数组而言，找到小于当前的元素，可以直接遍历，从左到右，找到第一个非升序或者非降序的元素，看该元素有何特征。

### 贪心/动态规划：[514. Freedom Trail](https://leetcode-cn.com/problems/freedom-trail/)

思路：一开始认为应该用贪心来解，但是贪心有本质上的缺点，它和动态规划的区别在于，贪心如果是正确的解，那么它每一步的解应该都能证明是最优解。如果是动态规划的话，前面的解不一定是最优解，我可以用数组来存储当前状态下的解，一步步递进求出全局最优解。

### 单调栈：[402. Remove K Digits](https://leetcode-cn.com/problems/remove-k-digits/)

单调栈主要就是维护数组元素的单调关系，最常见的操作就是不断地pop和push，如下：

**对于每个数字，如果该数字小于栈顶元素，我们就不断地弹出栈顶元素，直到**

- **栈为空**
- **或者新的栈顶元素不大于当前数字**
- **或者我们已经删除了k位数字**

不同于优先队列（堆），这里的栈顶元素：

- 是序列的前一个元素（pop操作之后的）
- 也是前面的元素的最大值/最小值
- 保持了序列中的相对前后关系

单调栈一般可以保持在O(n)的时间复杂度内。

