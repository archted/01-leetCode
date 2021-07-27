# Union-Find

## 一、概念

Union-Find 算法，也就是常说的并查集算法，主要是解决图论中「动态连通性」问题的。

现在我们的 Union-Find 算法主要需要实现这两个 API：

```java
class UF {
    /* 将 p 和 q 连接 */
    public void union(int p, int q);
    /* 判断 p 和 q 是否连通 */
    public boolean connected(int p, int q);
    /* 返回图中有多少个连通分量 */
    public int count();
}
```

这里所说的「连通」是一种等价关系,具有「自反性」「传递性」和「对称性」。

判断这种「等价关系」非常实用，比如说编译器判断同一个变量的不同引用，比如社交网络中的朋友圈计算等等。Union-Find 算法的关键就在于`union`和`connected`函数的效率。那么用什么模型来表示这幅图的连通状态呢？用什么数据结构来实现代码呢？

怎么用森林来表示连通性呢？我们设定树的每个节点有一个指针指向其父节点，如果是根节点的话，这个指针指向自己。比如说刚才那幅 10 个节点的图，一开始的时候没有相互连通，就是这样：

![image-20210618101054608](assets/image-20210618101054608.png)

**如果某两个节点被连通，则让其中的（任意）一个节点的根节点接到另一个节点的根节点上。如果节点`p`和`q`连通的话，它们一定拥有相同的根节点。**

![image-20210618101243140](assets/image-20210618101243140.png)

## 二、基本思路

算法的关键点有 3 个：

1、用 `parent` 数组记录每个节点的父节点，相当于指向父节点的指针，所以 `parent` 数组内实际存储着一个森林（若干棵多叉树）。

2、用 `size` 数组记录着每棵树的重量，目的是让 `union` 后树依然拥有平衡性，而不会退化成链表，影响操作效率。

3、在 `find` 函数中进行路径压缩，保证任意树的高度保持在常数，使得 `union` 和 `connected` API 时间复杂度为 O(1)。

路径压缩：

 ![image-20210618101944398](assets/image-20210618101944398.png)

![image-20210618101955497](assets/image-20210618102035854.png)



![image-20210618102109500](assets/image-20210618102109500.png)

## 三、带路径压缩的代码模板

```python
class UF:
    def __init__(self, M):
        self.parent = {}
        self.size = {}// 记录每棵树包含的节点数，表示树的“重量”，比如size[3]=5，表示以节点3为根的那棵树，总共有5个节点。
        self.count = 0 // 连通分量个数
        # 初始化 parent，size 和 count
        for i in range(M):
            self.parent[i] = i
            self.count += 1
            self.size[i] = 1

    def find(self, x):
        if x == self.parent[x]:
			return x
		self.parent[x] = self.find(self.parent[x])
		return self.parent[x]
    def union(self, p, q):
        if self.connected(p, q): return
        # 小的树挂到大的树上， 使树尽量平衡
        leader_p = self.find(p)
        leader_q = self.find(q)
        if self.size[leader_p] < self.size[leader_q]:
            self.parent[leader_p] = leader_q
            self.size[leader_q] += self.size[leader_p]
        else:
            self.parent[leader_q] = leader_p
            self.size[leader_p] += self.size[leader_q]
        self.count -= 1
    def connected(self, p, q):
        return self.find(p) == self.find(q)
    def count(self):
    	return self.count;
```

Union-Find 算法的复杂度可以这样分析：构造函数初始化数据结构需要 O(N) 的时间和空间复杂度；连通两个节点`union`、判断两个节点的连通性`connected`、计算连通分量`count`所需的时间复杂度均为 O(1)。

java版本：

```java
class UF {
    // 连通分量个数
    private int count;
    // 存储一棵树
    private int[] parent;
    // 记录树的“重量”
    private int[] size;

    public UF(int n) {
        this.count = n;
        parent = new int[n];
        size = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
            size[i] = 1;
        }
    }

    public void union(int p, int q) {
        int rootP = find(p);
        int rootQ = find(q);
        if (rootP == rootQ)
            return;

        // 小树接到大树下面，较平衡
        if (size[rootP] > size[rootQ]) {
            parent[rootQ] = rootP;
            size[rootP] += size[rootQ];
        } else {
            parent[rootP] = rootQ;
            size[rootQ] += size[rootP];
        }
        count--;
    }

    public boolean connected(int p, int q) {
        return find(p) == find(q);
    }

    private int find(int x) {
        while (parent[x] != x) {
            // 进行路径压缩
            parent[x] = parent[parent[x]];
            x = parent[x];
        }
        return x;
    }

    public int count() {
        return count;
    }
}
```



## 四、带权并查集

比如：

```js
a    b
^    ^
|    |
|    |
x    y
```

如上表示的是 x 的父节点是 a，y 的父节点是 b，现在我需要将 x 和 y 进行合并。



```js
a    b
^    ^
|    |
|    |
x -> y
```

假设 x 到 a 的权重是 w(xa)，y 到 b 的权重为 w(yb)，x 到 y 的权重是 w(xy)。合并之后会变成如图的样子：



```js
a -> b
^    ^
|    |
|    |
x    y
```

那么 a 到 b 的权重应该被更新为什么呢？我们知道 w(xa) + w(ab) = w(xy) + w(yb)，也就是说 a 到 b 的权重 w(ab) = w(xy) + w(yb) - w(xa)。

```python
class UF:
    def __init__(self, M):
        # 初始化 parent，weight
        self.parent = {}
        self.weight = {}
        for i in range(M):
            self.parent[i] = i
            self.weight[i] = 0

   def find(self, x):
        if self.parent[x] != x:
            ancestor, w = self.find(self.parent[x])
            self.parent[x] = ancestor
            self.weight[x] += w
        return self.parent[x], self.weight[x]
    def union(self, p, q, dist):
        if self.connected(p, q): return
        leader_p, w_p = self.find(p)
        leader_q, w_q = self.find(q)
        self.parent[leader_p] = leader_q
        self.weight[leader_p] = dist + w_q - w_p
    def connected(self, p, q):
        return self.find(p)[0] == self.find(q)[0]
```

## 应用

### 一、DFS 的替代方案

很多使用 DFS 深度优先算法解决的问题，也可以用 Union-Find 算法解决。

#### [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

![image-20210618103558849](assets/image-20210618103558849.png)

1.DFS

问题可以转化为寻找和边界联通的`O`？

具体做法是：先用 for 循环遍历棋盘的**四边**，用 DFS 算法把那些与边界相连的 `O` 换成一个特殊字符，比如 `#`；然后再遍历整个棋盘，把剩下的 `O` 换成 `X`，把 `#` 恢复成 `O`。这样就能完成题目的要求，时间复杂度 O(MN)。

```java
class Solution {
    boolean[][] visited;

    public void solve(char[][] board) {
        int row = board.length;
        int column = board[0].length;
        visited= new boolean[row][column];
        // 将所有与边界O联通的O都染色为#
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < column; j++) {
                if ((i == 0 || i == row - 1 || j == 0 || j == column - 1) && board[i][j] == 'O') { 
                    dfs(i, j, row, column, board);
                }
            }
        }
        // 剩下的O是不符合染色条件的 
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < column; j++) {
                if (board[i][j] == 'O') {
                    board[i][j] = 'X';
                }
                if (board[i][j] == '#') { // 还原矩阵
                    board[i][j] = 'O';
                }
            }
        }

    // 将所有符合条件的O染色成#
    void dfs(int i, int j, int row, int column, char[][] board) {
        // board[i][j] == '#' 说明已经搜索过了. 
        if (i < 0 || i > row - 1 || j < 0 || j > column - 1 || || board[i][j] == 'X' || board[i][j] == '#') {
            return;
        }
        board[i][j] = '#';
        dfs(i + 1, j, row, column, board);
        dfs(i - 1, j, row, column, board);
        dfs(i, j + 1, row, column, board);
        dfs(i, j - 1, row, column, board);
    }
}
```

2.并查集

实现复杂一些，甚至效率也略低，但这是使用 Union-Find 算法的通用思想，值得一学。

思路：**主要思路是适时增加虚拟节点，想办法让元素「分门别类」，建立动态连通关系**。

在本题中，具体做法是你可以把那些不需要被替换的 `O` 看成一个拥有独门绝技的门派，它们有一个共同祖师爷叫 `dummy`，这些 `O` 和 `dummy` 互相连通，而那些需要被替换的 `O` 与 `dummy` 不连通。

![image-20210618141333302](assets/image-20210618141333302.png)

tips:二维坐标 `(x,y)` 可以转换成 `x * n + y` 这个数（`m` 是棋盘的行数，`n` 是棋盘的列数）。敲黑板，**这是将二维坐标映射到一维的常用技巧**。

```java
class Solution {

    public void solve(char[][] board) {
        int row = board.length;
        int column = board[0].length;
        UF uf = new UF(row * column + 1);
        int dummy = row * column;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < column; j++) {
                int idx = column * i + j;
                if ((i == 0 || i == row - 1 || j == 0 || j == column - 1) && board[i][j] == 'O') {
                    uf.union(idx, dummy);
                }
            }
        }
        // 方向数组 d 是上下左右搜索的常用手法
        int[][] d = new int[][] { { 1, 0 }, { 0, 1 }, { 0, -1 }, { -1, 0 } };
        for (int i = 1; i < row - 1; i++) {
            for (int j = 1; j < column - 1; j++) {
                if (board[i][j] == 'O') {
                    // 将此 O 与上下左右的 O 连通
                    for (int k = 0; k < 4; k++) {
                        int x = i + d[k][0];
                        int y = j + d[k][1];
                        if (board[x][y] == 'O')
                            uf.union(x * column + y, i * column + j);
                    }
                }
            }
        }
        // 所有不和 dummy 连通的 O，都要被替换
        for (int i = 1; i < row - 1; i++) {
            for (int j = 1; j < column - 1; j++) {
                if (!uf.connected(dummy, i * column + j)) {
                    board[i][j] = 'X';
                }
            }
        }
    }

}

```



#### [990. 等式方程的可满足性](https://leetcode-cn.com/problems/satisfiability-of-equality-equations/)

![image-20210618103648558](assets/image-20210618103648558.png)

![image-20210618103659905](assets/image-20210618103659905.png)

分析题意后发现，就差直接报并查集名字了。

```java
boolean equationsPossible(String[] equations) {
    // 26 个英文字母
    UF uf = new UF(26);
    // 先让相等的字母形成连通分量
    for (String eq : equations) {
        if (eq.charAt(1) == '=') {
            char x = eq.charAt(0);
            char y = eq.charAt(3);
            uf.union(x - 'a', y - 'a');
        }
    }
    // 检查不等关系是否打破相等关系的连通性
    for (String eq : equations) {
        if (eq.charAt(1) == '!') {
            char x = eq.charAt(0);
            char y = eq.charAt(3);
            // 如果相等关系成立，就是逻辑冲突
            if (uf.connected(x - 'a', y - 'a'))
                return false;
        }
    }
    return true;
}
```

### 检测图是否有环

思路： 只需要将边进行合并，并在合并之前判断是否已经联通即可，如果合并之前已经联通说明存在环。

代码：

```c
uf = UF()
for a, b in edges:
    if uf.connected(a, b): return False
    uf.union(a, b)
return True
```

题目推荐：

- [684. 冗余连接](https://leetcode-cn.com/problems/redundant-connection/solution/bing-cha-ji-mo-ban-ben-zhi-jiu-shi-jian-0wz2m/)
- [Forest Detection](https://binarysearch.com/problems/Forest-Detection)
- 最小生成树经典算法 Kruskal



几道并查集的题目：

- [547. 朋友圈]()
- [721. 账户合并](https://leetcode-cn.com/problems/accounts-merge/solution/mo-ban-ti-bing-cha-ji-python3-by-fe-lucifer-3/)
- [990. 等式方程的可满足性](https://github.com/azl397985856/leetcode/issues/304)
- [1202. 交换字符串中的元素](https://leetcode-cn.com/problems/smallest-string-with-swaps/)
- [1697. 检查边长度限制的路径是否存在]