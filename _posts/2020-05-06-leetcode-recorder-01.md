---
layout: post
title:  "leetcode algorithm-01"
date:   2020-05-06
categories: study
tags: Leetcode, Sort
subtitle: dynamic programming, sort
---

## 2020/05/06

### **动态规划**

动态规划的题思路：

- 确定$dp[i][j][k]...$代表的含义，包括$i,j,k$代表的index是哪些部分。
- 转化到当前状态的最优子结构是什么。
- 状态转移方程是什么。

**题目1：[最低票价](https://leetcode-cn.com/problems/minimum-cost-for-tickets)**

思路：动态规划，有两种思路。

（1）该题从后往前建立最优解，即dp[i]表示第i天开始玩的最低票价，且dp[last-day]=cost[0];

状态转移方程为：dp[i] = min(cost[0]+dp[i+1], cost[1]+dp[i+7], cost[2]+dp[i+30])​

（2）从前往后建立最优解，即dp[i]表示到第i天结束的最低票价，包括第i天需要通信证和第i天不需要通行证的情况。

状态转移方程为：

- 需要：dp[i] = min(dp[i - 1] + cost[0], dp[i - 7] + cost[1], dp[i - 30] + cost[2])

- 不需要：dp[i] = dp[i - 1]；

### 排序总结

主要对几种排序的思路进行梳理，只是初步的一个分析，后续会再次修改。

注1：只有比较排序，才会有$\Omega(n \log n)$的限制。

注2：c++版本见[github](https://github.com/cxcacac/Algorithms-implementation/tree/master/cpp_version/sort%20algorithm)。

- **选择排序（Selection Sort）**

  思路：暴力解法。设置最小值，遍历比较，换位置，时间复杂度$O(n^2)$

  python:

  ```python
  def selection_sort(nums):
        for i in range(len(nums)):
            for j in range(i,len(nums)):
                if nums[i]>nums[j]:
                    nums[i],nums[j] = nums[j],nums[i]
        return nums
  ```

- **插入排序(Insertion Sort)**

  思路：从前往后逐渐排序，选取未排序的子序列的第一个元素移动插入到排序好的子序列中合适的位置。时间复杂度$O(n^2)$

  ```python
  def insert_sort(nums):
        for i in range(len(nums)):
            while i>0 and nums[i]<nums[i-1]:
                nums[i - 1], nums[i] = nums[i], nums[i - 1]
                i -= 1
        return nums
  ```

- **冒泡排序（Bubble Sort）**

  思路：从后往前逐渐排序，大元素沉到数组的最后面。时间复杂度$O(n^2)$

  python：

  ```python
  def bubble_sort(nums):
      # i表示多个元素bubble，不表示位置。
        for i in range(len(nums)):
            for j in range(1,len(nums)-i):
                if nums[j-1]>nums[j]:
                    nums[j-1],nums[j] = nums[j],nums[j-1]
        return nums
  ```

- **归并排序（Merge Sort）**

  思路：采用分治法的思路，将序列分为最小子序列，然后一层层合并两个有序数组。时间复杂度$O(n\log n)$。注意分治法需要确定最小子单元的终止条件，例如left<right，或者len(nums)==1. 

  python

  ```python
  def merge_sort(nums):
        if len(nums) <= 1:
            return nums
        mid = len(nums) // 2
        left = merge_sort(nums[:mid])
        right = merge_sort(nums[mid:])
        # 取切片效率低,也可以传入两个两个参数，将left<right做为终止判定条件。
        return merge(left, right)

    def merge(left,right):
        # 利用双指针法进行合并
        res =[]
        i,j = 0,0
        while i < len(left) and j < len(right):
            if left[i] <= right[j]:
            res.append(left[i])
                i += 1
            else:
                res.append(right[j])
                j += 1
        res += left[i:]
        res += right[j:]
        return res
  ```

- **快速排序（Quick Sort）**

  思路：将数组分为两个部分，分别排序，最后组合。从数组中选取一个基准数（pivot），大于和小于pivot的数组分为两个部分（Partition），然后递归一直partition后合并，有点像paired-comparison。该算法时间复杂度为$O(n \log n)$。

  python:

  ```python
  def quicksort(nums):
        if len(nums)<=1:
            return nums
        pivot = nums[len(nums)//2]
        left = [x for x in nums if x < pivot]
        middle = [x for x in nums if x==pivot]
        right = [x for x in nums if x>pivot]
        return quicksort(left)+middle+quicksort(right)
  ```

- **希尔排序（Shell sort）**

  思路：针对插入排序只能将数据和邻位来比较移动一位的低效方式，希尔排序提高了移动的步长。该算法选择一个合适的增量序列，增量序列的最后一个元素为1，就是普通的插入排序。但是与普通的插入排序相比，步长较大的排序会做大部分的工作，从而降低时间复杂度。希尔排序的时间复杂度和增量序列的选择有关。可以从$O(n^{1.3})-O(n^2)$

  python：

  ```python
  def shell_sort(nums):
        n = len(nums)
        gap = n//2
        while gap:
            for i in range(gap, n):
                while i-gap>=0 and nums[i-gap]>nums[i]:
                nums[i - gap], nums[i] = nums[i], nums[i - gap]
                    i -= gap
        gap//=2
        return nums
  ```

- **堆排序（Heap Sort）**

  思路：依照数组建堆，并把数组转换成最大堆（Max-Heap Heap），重复从最大堆中取出数值最大的结点。时间复杂度为$O(n \log n)$，建堆后主要分为以下几个步骤：

  （1）最大堆调整，使子节点永远小于父节点从而建立最大堆

  （2）交换堆顶和堆底元素，移出根节点，之后继续进行最大堆调整。

  注：对堆节点的访问，在数组起始位置为0时，满足

  - 父节点$i \rightarrow 2i+1（2i+2）$左（右）子节点。

  - 子节点$i \rightarrow \lfloor (i-1)/2 \rfloor$父节点。

  python：

  ```python
  def heap_sort(nums):
        def sift_down(start,end):
            # 对start元素在end节点之前找到合适的位置。
            root = start
            child = 2*root + 1
            while child<=end:
                if child + 1 <= end and nums[child] < nums[child+1]:
                    child += 1
                if nums[root]<nums[child]:
                    nums[root],nums[child] = nums[child],nums[root]
                    root = child
                    child = 2*root + 1
                else:
                    break
                # 这个地方也可以写成递归写法，一样的道理，就是重复执行。           
        # 从底部开始排序得到最大堆
        for start in range((len(nums)-2)//2,-1,-1):
            sift_down(start,len(nums)-1)
        # 取出堆顶元素，再次调整置换后的堆顶元素的位置。
        for end in range(len(nums)-1,-1,-1):
            nums[0],nums[end] = nums[end],nums[0]
            sift_down(0,end-1)
        return nums
  ```

- **基数排序（Radix Sort）**

  思路：对正整数，将所有的数统一为一样的数位长度，短数补零。从最后的（最低位）开始排序到最高位数。由于该算法比较依赖整数的位数，所以时间复杂度为$O(kn)$，其中k为位数，n为元素个数。

  python：

  ```python
  import math
  def radix_sort(nums, radix=10):
        # 求该数组的最大数的位数
        k = int(math.ceil(math.log(max(nums)+1, radix)))
        for i in range(1,k+1):
            bucket = [[] for j in range(radix)]
            for num in nums:
                bucket[val%(radix**i)//(radix**(i-1))].append(val)
            nums = []
            for arr in bucket:
                nums.extend(arr)
        return nums
  ```

- **计数排序**

  思路：开辟额外的内存空间来存储数组元素，即通过一个最大值-最小值的数组来放置原来数组的元素。该算法的时间复杂度为$O(n+k)$，其中n为数组元素个数，k为最大最小元素差值。

  python：

  ```python
  def counting_sort(nums):
        n,l,h = len(nums),min(nums),max(nums)
        temp = [0 for i in range(h-l)]
        for num in nums:
            temp[num-l] += 1
        j = 0
        for i in range(n):
            while j < len(temp) and temp[j] == 0:
                j += 1
            nums[i] = j + l
            temp[j] -= 1
        return nums
  ```

- **桶排序**

  思路：如果排序的数组是均匀分布的，可以将数组分配到有限数量的桶中，对每个桶在进行排序，采用别的算法或者递归方式。桶排序其实是聚合的计数排序，复杂度是一样的。

  python:

  ```python
  def bucket_sort(nums,bucketSize):
        l,h = min(nums), max(nums)
        bucketNum = (h-l)//bucketSize + 1
        buckets = [[] for i in range(bucketNum)]
        res = []
        for num in nums:
            buckets[(num - l) // bucketSize].append(num)
        for bucket in buckets:
            if bucket == None: 
                continue
            else:
                res.extend(bucket_sort(bucket, bucketSize))
        return res
  ```

- **Bogo排序**

  思路：将所有数字混乱一次，然后检查是否排列好，否则就再混乱一次。

  ```python
    def bogo_sort(my_list):
        while not in_order(my_list):
            shuffle(my_list)
  ```



