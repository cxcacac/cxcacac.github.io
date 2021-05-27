### 动态规划+二分

三道题：

[354. 俄罗斯套娃信封问题](https://leetcode-cn.com/problems/russian-doll-envelopes/)、[673. 最长递增子序列的个数](https://leetcode-cn.com/problems/number-of-longest-increasing-subsequence/)。

[646. Maximum Length of Pair Chain](https://leetcode-cn.com/problems/maximum-length-of-pair-chain/)

### Robin-karp, manacher, KMP算法

其中kmp的模板可以看下面的：

https://leetcode-cn.com/problems/shortest-palindrome/solution/zui-duan-hui-wen-chuan-by-leetcode-solution/

关于c++，这种写法：

```c++
int ViolentMatch(char* s, char* p)
{
	int sLen = strlen(s);
	int pLen = strlen(p);
 
	int i = 0;
	int j = 0;
	while (i < sLen && j < pLen)
	{
		if (s[i] == p[j])
		{
			//①如果当前字符匹配成功（即S[i] == P[j]），则i++，j++    
			i++;
			j++;
		}
		else
		{
			//②如果失配（即S[i]! = P[j]），令i = i - (j - 1)，j = 0    
			i = i - j + 1;
			j = 0;
		}
	}
	//匹配成功，返回模式串p在文本串s中的位置，否则返回-1
	if (j == pLen)
		return i - j;
	else
		return -1;
}
```

kmp的next数组求法：


```php
private function getNext($word): array
{
    $next = [-1];
    $len = strlen($word);
    $k = -1;
    $j = 0;
    while ($j < $len - 1) {
        if ($k == -1 || $word[$j] == $word[$k]) {
            $next[++$j] = ++$k;
        } else {
            $k = $next[$k];
        }
    }
	return $next;
}
```

### 有限状态自动机

确定有限状态自动机（以下简称「自动机」）是一类计算模型。它包含一系列状态，这些状态中：

有一个特殊的状态，被称作「初始状态」。
还有一系列状态被称为「接受状态」，它们组成了一个特殊的集合。其中，一个状态可能既是「初始状态」，也是「接受状态」。
起初，这个自动机处于「初始状态」。随后，它顺序地读取字符串中的每一个字符，并根据当前状态和读入的字符，按照某个事先约定好的「转移规则」，从当前状态转移到下一个状态；当状态转移完成后，它就读取下一个字符。当字符串全部读取完毕后，如果自动机处于某个「接受状态」，则判定该字符串「被接受」；否则，判定该字符串「被拒绝」。

如果输入的过程中某一步转移失败了，即不存在对应的「转移规则」，此时计算将提前中止。在这种情况下我们也判定该字符串「被拒绝」。

https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/

这道题后面的有限自动学习机的代码很有意思，可以看一下。

### 顺序与派别

比如1，2，3，4，5，6，从一开始。 其中:

- 1，2属于派系1.
- 3，4属于派系2
- 按照顺序划分，假设每个派系有k个元素，如何判断该元素属于什么派系？

```
--n;
res = n/k + 1;
```

### 反转链表

pre代表前面的链的第一个节点，head代表后面的链的第一个节点，通过训练加一个head节点到pre节点前面，并且重新赋值。

最后pre节点为头结点的链为反转之后的链。

```c++
ListNode* pre=nullptr;
while(head){
    ListNode* next = head.next;
    head.next = pre;
    pre = head;
    head = next;
}
```

### 位运算与幂运算

[剑指 Offer 16. 数值的整数次方 LCOF](https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)

这里面还有一个命题是关于int32的范围。

-2147483648这个指数也很有意思呢，如果为了用移位取代除法来加速，负数是用补码表示的，补码的除法逻辑又不能用移位来解决...就需要取n的绝对值...因为int的取值范围是-2147483648到2147483647...所以java的Math.abs(n)在这个时候返回的还是-2147483648

Java 代码中 int32 变量n∈[−2147483648,2147483647] ，因此当 n = -2147483648 时执行 n=−n 会因越界而赋值出错。解决方法是先将 n 存入 long 变量 b ，后面用 b 操作即可。

### tips:

#### c++定义堆中的比较规则

优先队列的使用方式。

priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(&cmp)> q(cmp);

#### java中关于堆的处理方式：

其中包括priorityqueue的poll与offer，map的遍历。

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> occurrences = new HashMap<Integer, Integer>();
        for (int num : nums) {
            occurrences.put(num, occurrences.getOrDefault(num, 0) + 1);
        }

        // int[] 的第一个元素代表数组的值，第二个元素代表了该值出现的次数
        PriorityQueue<int[]> queue = new PriorityQueue<int[]>(new Comparator<int[]>() {
            public int compare(int[] m, int[] n) {
                return m[1] - n[1];
            }
        });
        for (Map.Entry<Integer, Integer> entry : occurrences.entrySet()) {
            int num = entry.getKey(), count = entry.getValue();
            if (queue.size() == k) {
                if (queue.peek()[1] < count) {
                    queue.poll();
                    queue.offer(new int[]{num, count});
                }
            } else {
                queue.offer(new int[]{num, count});
            }
        }
        int[] ret = new int[k];
        for (int i = 0; i < k; ++i) {
            ret[i] = queue.poll()[0];
        }
        return ret;
    }
}
```



