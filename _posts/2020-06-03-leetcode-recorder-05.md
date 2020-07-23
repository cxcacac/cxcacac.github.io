---
layout: post
title:  "leetcode algorithm-05"
date:   2020-06-03
categories: study
tags: Leetcode, graph algorithm
subtitle: Algorithm record:several graph algorithm.
---

# 刷题记录-0603

## 图的算法：[网络延迟时间](https://leetcode-cn.com/problems/network-delay-time/)

注：该Blog参考博主[hncboy](https://leetcode-cn.com/u/hncboy/)，[网页链接](https://leetcode-cn.com/problems/network-delay-time/solution/java-shi-xian-dijkstra-floyd-bellman-ford-spfa-by-/)。

这个网络延迟时间算是最短路径题的典型，这里对图的各种算法进行记录和python\java实现。包括：

- 深度优先遍历BFS+广度优先遍历DFS
- Dijkstra算法
- Bellman-Ford算法
- Floyd-Warshall算法
- SPFA算法

### **深度优先遍历**

在深度遍历中，没有`visited`这一项，但是对图的权重加了个`relaxation`，其实就是一个比较，如果更改之后需要再更新，直到不能松弛为止，效率还是比较低的。时间复杂度应该是$O(n^n)$，带权图问题还是不要用深度优先遍历。

```python
class Solution:
    def networkDelayTime(self, times: List[List[int]], N: int, K: int) -> int:
        adj = [[] for i in range(N+1)]
        # 统计一层需要的最大时间。
        # 如果访问过，需要一个relaxation。
        distance = [float('inf') for i in range(N+1)]
        distance[0] = 0
        for time in times:
            adj[time[0]].append([time[1],time[2]])
        
        def dfs(start, curtime):
            # 
            if curtime >= distance[start]:
                return 
            distance[start] = curtime
            # 如果最小时间更新了，那么后面的节点也要更新。
            for item in adj[start]:
                # 这里还有回溯的思想。
                dfs(item[0],curtime+item[1])
                
        dfs(K,0)
        if max(distance) != float('inf'):
            return max(distance)
        else:
            return -1
```

### **广度优先遍历**

```python
class Solution:
    def networkDelayTime(self, times: List[List[int]], N: int, K: int) -> int:
        adj = [[] for i in range(N+1)]
        # 统计一层需要的最大时间。
        # 如果访问过，需要一个relaxation。
        distance = [float('inf') for i in range(N+1)]
        distance[0] = 0
        for time in times:
            adj[time[0]].append([time[1],time[2]])
        
        def bfs(start):
            distance[start] = 0
            queue = [start]
            while queue:
                temp = queue.pop(0)
                for node in adj[temp]:
                    if distance[temp] + node[1] < distance[node[0]]:
                        distance[node[0]] = distance[temp] + node[1]
                        queue.append(node[0])
                        # 如果没有更新，则无需再添加。
        bfs(K)
        if max(distance) != float('inf'):
            return max(distance)
        else:
            return -1
```

### **Dijkstra算法**

建立源点集`S={start...}`，选取距离最小的顶点`k`加入到S中，以`k`为新考虑的节点，对k相连的节点进行权重的比较，如更新的距离值小于原来的距离值，则修改路径。重复这个步骤直到所有节点加入到`S`中。

Dijkstra算法具有单向性，需要visited数组，而且需要数学证明。

```java
class Solution {
    public int networkDelayTime(int[][] times, int N, int K) {
        // 构建邻接矩阵，给边建立权重，这里采用数组直接声明
        // 邻接矩阵和邻接表不同，邻接矩阵一般采用直接便利的方式，不做BFS和DFS
        int[][] graph = new int[N+1][N+1];
        for(int i=1; i<=N; i++){
            for(int j=1; j<=N; j++){
                graph[i][j] = -1;
            }
        }
        for(int[] time:times){
            graph[time[0]][time[1]]=time[2];

        }
        int[] distance = new int[N+1];
        Arrays.fill(distance,-1);
        // 初始化连接出发节点的所有结点的距离。
        for(int i=1; i<=N; i++){
            distance[i] = graph[K][i];
        }
        distance[K] = 0;
        // 默认填false
        boolean[] visited = new boolean[N+1];
        visited[K] = true;
        // 遍历除K节点的所有节点
        for(int i=1; i<=N; i++){
            int minDistance = Integer.MAX_VALUE;
            int minIndex = 1;
            // 寻找据当前节点最近的节点
            for(int j=1; j<=N; j++){
                // 如果没有访问过，当前节点能到达下一个节点，则可以更新寻找最小值。
                if(!visited[j] && distance[j]!=-1 &&distance[j]<minDistance){
                    minDistance = distance[j];
                    minIndex = j;
                }
            }
            visited[minIndex] = true;
            for(int j=1; j<=N; j++){
                //对源节点相连的节点权重进行更新。
                if(graph[minIndex][j]!=-1){
                    if(distance[j] != -1){
                        // 这里只能说到该点的值是最小值，而不是对源集来说的最小距离。
                        distance[j] = Math.min(distance[j], distance[minIndex]+graph[minIndex][j]);
                    }else{
                        // 如果没有和源节点相连，则只进行路径的第一次赋值，表明可以被源集接触。
                        distance[j] = distance[minIndex] + graph[minIndex][j]; 
                    }
                }
            }
        }
        int Maxdistance = 0;
        for(int i=1; i<=N; i++){
            if(distance[i] == -1){
                return -1;
            }
            Maxdistance = Math.max(distance[i],Maxdistance);
        }
        return Maxdistance;
    }
}
```

### **Floyd算法**

思路：动态规划算法，对任意两点`i,j`间的路径有两种子状态，直接从`i`到`j`和从`i-k-j`两种状态，所以可以遍历中间节点，来松弛更新`i`到`j`的路径。

```java
class Solution {
    public int networkDelayTime(int[][] times, int N, int K) {
        // 构建邻接矩阵,初始化
        int[][] graph = new int[N+1][N+1];
        for(int i = 1; i < N + 1; i++){
            for(int j = 1; j < N + 1; j++){
                graph[i][j] = i==j? 0:-1;
            }
        }
        for(int[] time: times){
            graph[time[0]][time[1]] = time[2];
        }
        // 遍历所有节点，k代表中间节点,这里将采用k作为中间节点松弛
        for(int k = 1; k < N + 1; k++){
            for(int i = 1; i < N + 1; i++){
                for(int j= 1; j < N + 1; j++){
                    if(graph[i][k] != -1 && graph[k][j] != -1){
                        // 如果存在路径i-k-j路径，则进行松弛，否则建立路径。
                        if(graph[i][j] != -1){
                            graph[i][j] = Math.min(graph[i][j], graph[i][k] + graph[k][j]);
                        }else{
                            graph[i][j] = graph[i][k] + graph[k][j];
                        }
                    }
                }
            }
        }
        int maxDistance = 0;
        for(int i = 0; i < N + 1; i++){
            if(graph[K][i]==-1){
                return -1;
            }
            maxDistance = Math.max(maxDistance, graph[K][i]);
        }
        return maxDistance;
    }
}
```

### Bellman-ford算法

思路：对所有的边（节点）进行松弛，每次松弛都会得到一条最短路径，每次松弛`V-1`次，若可以无限松弛，则说明包含负环。

步骤：初始化图，有起点`s`到各个顶点的距离`dist(v)`，对每个边进行松弛操作，即$dist(u)+w(u,b)<dist(v)$，遍历完成后，继续遍历，直到无法进行松弛为止。有点类似于循环BFS，是双向的，而Dijkstra算法效率相对来说就比较高，但是需要更好的数学证明。

```java
class Solution {
    public int networkDelayTime(int[][] times, int N, int K) {
        // 构建邻接矩阵，给边建立权重，这里采用数组直接声明
        // 邻接矩阵和邻接表不同，邻接矩阵一般采用直接遍历的方式，不做BFS和DFS
        int[] distance = new int[N+1];
        //由于是最大化最短路径，而且值都是大于零的，所以这里所有点的距离取-1就可以。
        Arrays.fill(distance,-1);
        distance[K] = 0;
        // 进行松弛，每次总进行N-1次松弛操作。
        for(int i = 1; i < N; i++){
            for(int[] time: times){
                int u = time[0];
                int v = time[1];
                int w = time[2];
                if(distance[u] != -1){
                    if(distance[v] != -1){
                        distance[v] = Math.min(distance[v], distance[u] + w);
                    } else {
                        distance[v] = distance[u] + w;
                    }
                }
            }
        }
        int maxDistance = 0;
        for(int i = 1; i < N + 1; i++){
            if(distance[i] == -1){
                return -1;
            }
            maxDistance = Math.max(distance[i],maxDistance);
        }
        return maxDistance;
    }
}
```

### SPFA算法(Moore-Bellman-Ford算法)

思路：SPFA算法的基本思路与贝尔曼-福特算法相同，即每个节点都被用作用于松弛其相邻节点的备选节点。相较于贝尔曼-福特算法，SPFA算法的提升在于它并不盲目尝试所有节点，而是维护一个备选节点队列，并且仅有节点被松弛后才会放入队列中。整个流程不断重复直至没有节点可以被松弛。

针对Bellman-Ford简化了很多，这里只更新原来已经更新过的，类似于有限的BFS，最坏情况和Bellman-Ford算法相同的。

```java
class Solution {
    public int networkDelayTime(int[][] times, int N, int K) {
        int[][] graph = new int[N+1][N+1];
        for(int i = 1; i < N + 1; i++){
            for(int j = 1; j < N + 1; j++){
                graph[i][j] = i==j? 0:-1;
            }
        }
        for(int[] time: times){
            graph[time[0]][time[1]] = time[2];
        }
        // 按照 bellman-ford的思路，以k为原点。
        int[] distance = new int[N+1];
        Arrays.fill(distance, -1);
        distance[K] = 0;

        Queue<Integer> queue = new LinkedList<>();
        queue.add(K);
        while(!queue.isEmpty()){
            int curr = queue.poll();
            for(int i = 1; i < N + 1; i++){
                // 依照当前已经更新过的节点进行松弛，如果可以更改路径，则添加到队列中。
                if(graph[curr][i]!=-1 && (distance[i] == -1 || distance[i] > distance[curr]+graph[curr][i])){
                    distance[i] = distance[curr] + graph[curr][i];
                    if(!queue.contains(i)){
                        queue.add(i);
                    }
                }
            }
        }
        int maxDistance = 0;
        for (int i = 1; i <= N; i++) {
            if (distance[i] == -1) {
                return -1;  
            }
            maxDistance = Math.max(distance[i], maxDistance);
        }
        return maxDistance;
    }
```

