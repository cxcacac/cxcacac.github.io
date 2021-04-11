### Morris遍历算法

比如中序遍历：

```c++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        TreeNode *predecessor = nullptr;

        while (root != nullptr) {
            if (root->left != nullptr) {
                // predecessor 节点就是当前 root 节点向左走一步，然后一直向右走至无法走为止
                predecessor = root->left;
                while (predecessor->right != nullptr && predecessor->right != root) {
                    predecessor = predecessor->right;
                }
                
                // 让 predecessor 的右指针指向 root，继续遍历左子树
                if (predecessor->right == nullptr) {
                    predecessor->right = root;
                    root = root->left;
                }
                // 说明左子树已经访问完了，我们需要断开链接
                else {
                    res.push_back(root->val);
                    predecessor->right = nullptr;
                    root = root->right;
                }
            }
            // 如果没有左孩子，则直接访问右孩子
            else {
                res.push_back(root->val);
                root = root->right;
            }
        }
        return res;
    }
};
```

### 并查集+有向图

#### [Redundant Connection II](https://leetcode-cn.com/problems/redundant-connection-ii/)

思路：主要是有向图的话，有两种类型的边要移除，这是需要考虑清楚的。

​    // 不一定有环出现，也不一定有冲突出现，但只能有一条边被移除而恢复正常，所以冲突的边和环边有一个节点是重合的。

​    // 另外，冲突的边不可能出现两个不同的节点上，否则不满足只移除一条边的限制。

​    // 这里是两类，所以一定要判断以下，是冲突还是环，而不是放在一个unionfind里，对无向图是有用的，无向图是只要连接就会归到同一个派别。

```c++
struct UnionFind {
    vector <int> ancestor;

    UnionFind(int n) {
        ancestor.resize(n);
        for (int i = 0; i < n; ++i) {
            ancestor[i] = i;
        }
    }
    int find(int index) {
        return index == ancestor[index] ? index : ancestor[index] = find(ancestor[index]);
    }

    void merge(int u, int v) {
        ancestor[find(u)] = find(v);
    }
};

class Solution {
public:
    vector<int> findRedundantDirectedConnection(vector<vector<int>>& edges) {
        // 特殊之处在于图是directed graph.
        int cntsize = edges.size();
        UnionFind uf = UnionFind(cntsize+1);
        auto parent = vector<int>(cntsize+1);
        for(int i = 1; i < cntsize+1; i++){
            parent[i] = i;
        }
        int conflict = -1;
        int cycle = -1;
        for(int i = 0; i < cntsize; i++){
            int x = edges[i][0], y = edges[i][1];
            if(parent[y] != y){
                conflict = i;
            }else{
                parent[y] = x;
                if(uf.find(x) == uf.find(y)){
                    cycle = i;
                }else{
                    uf.merge(x, y);
                }
            }
        }
        if(conflict == -1){
            return {edges[cycle][0], edges[cycle][1]};
        }else{
            if(cycle == -1){
                return {edges[conflict][0], edges[conflict][1]};
            }else{
                // 如果存在cycle，最后要移除的边不一定是最后一条形成环的边，因为与其他节点的关系未知，而是冲突边(因为只能保留一条冲突边。)
                return {parent[edges[conflict][1]], edges[conflict][1]};
            }
        }
    }
};
```

### 动态规划：[Binary Tree Cameras](https://leetcode-cn.com/problems/binary-tree-cameras/)

思路：当一个节点的状态与父节点和子节点（在数组中就是前面的状态和后面的状态会同时作用于当前的状态），该节点的状态一般有三个，其一是选择1，其二是被子节点的1影响，其三是被父节点的1影响，子节点没有作用。

### 链表：[ Linked List Cycle II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

思路：   

- ----a(初始点) ----b(相遇点)，fast走的路程(2a+2b)-slow走的路程(a+b)=a+b，所以再走a可以到初始点。

- 现在的slow的位置，和指针head的位置，同时走，可以在初始点相遇。
- （重要）在链表中，长度是通过指针的相遇（快慢指针）表示的，而不是一个特定的数。

### 链表：[Populating Next Right Pointers in Each Node II](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/)

思路：

递归是从右侧往左递归，逐步建立连接，寻找下一个节点的过程也是使用递归，这个DFS和BFS一样写的漂亮。

同时，从题目中也可以看到，c++和python类的构造函数逻辑不太相同，一个是重写函数，一个是设置构造函数的默认值。

两种方法同样都是对子结点进行处理，而不是对当前节点进行处理：

DFS：

```c++
class Solution {
public:
    Node* connect(Node* root) {
        // DFS并不是分治的思想，因为需要连接不同树的节点。
        // BFS很简单，最主要的是constant extra space.
        // 可以从右侧进行BFS建立连接，有点dp的意味
        if(!root) return root;
        if(root->left){
            if(root->right){
                root->left->next = root->right;
            }
            else{
                root->left->next = next(root->next);
            }
        }
        if(root->right){
            root->right->next = next(root->next);
        }
        connect(root->right);
        connect(root->left);
        return root;
    }
    // 函数名字写的不好，不应该是next，而应该是getnode
    Node* next(Node* root){
        if(!root) return NULL;
        if(root->left) return root->left;
        if(root->right) return root->right;
        return next(root->next);
        // if(root->next) return next(root->next);
        // return NULL;
    }
};
```

在链表中，存元素可以在这里做一个head节点，然后用head.next来找到元素，处理方法是很高明的。

BFS:

```PYTHON
class Solution:
    def connect(self, root: 'Node') -> 'Node':
        # 在当前层将下一层的节点连接，高
        # 建立了单向连接，所以只能从左到右
        left_most = root
        while left_most:
            cur = left_most
            # cur再上一层，pre在下一层
            head = pre = Node()
            while cur:
                if cur.left:
                    pre.next = cur.left
                    pre = pre.next
                if cur.right:
                    pre.next = cur.right
                    pre = pre.next
                cur = cur.next
            # 怎么换层？
            left_most = head.next
        return root
```

### 二分法：[Koko Eating Bananas](https://leetcode-cn.com/problems/koko-eating-bananas/)

Note：int mi = lo + (hi - lo) / 2; 在lo和hi都比较大的时候，会导致越界的问题。

这里面还有划分派别的问题：

如果要给1~k的元素都划分到第1个派别，采用式子$n=(m-1)/k+1$，解决这种时间的问题是最好用的，因为一个小时就算没用完也使用了。

```c++
class Solution {
public:
    int minEatingSpeed(vector<int>& piles, int H) {
        int lo = 1, hi = pow(10, 9);
        while (lo < hi) {
            int mi = lo + (hi - lo) / 2;
            if (!possible(piles, H, mi))
                lo = mi + 1;
            else
                hi = mi;
        }

        return lo;
    }

    bool possible(vector<int>& piles, int H, int K) {
        int time = 0;
        for (int p: piles)
            time += (p - 1) / K + 1;
        return time <= H;
    }
};
```

### 动态规划：[LCP 19. 秋叶收藏集](https://leetcode-cn.com/problems/UlBDOe/)

思路：动态规划还是最重要的两个问题：

- 状态的表示，用什么来表示一个状态，dp\[i][j]表示什么意思？
- 状态的转移关系，是如何利用子状态建立起来当前状态的？

在这个题中，dp\[i][j]可以表示为当前的位置为i处于j状态时的最小个数，j可以分为三个状态，即前面的红，后面的红，中间的黄，是按照顺序划分的，并不是按照种类划分的。