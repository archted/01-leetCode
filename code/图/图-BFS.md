# BFS

## 框架

BFS 的核心思想应该不难理解的，就是把一些问题抽象成图，从一个点开始，向四周开始扩散。一般来说，我们写 BFS 算法都是用「队列」这种数据结构，每次将一个节点周围的所有节点加入队列。

BFS 相对 DFS 的最主要的区别是：**BFS 找到的路径一定是最短的，但代价就是空间复杂度比 DFS 大很多.**

一、算法框架

**BFS本质上就是一幅「图」，让你从一个起点，走到终点，问最短路径**.

这个广义的描述可以有各种变体，比如走迷宫，有的格子是围墙不能走，从起点到终点的最短距离是多少？如果这个迷宫带「传送门」可以瞬间传送呢？

再比如说两个单词，要求你通过某些替换，把其中一个变成另一个，每次只能替换一个字符，最少要替换几次？

再比如说连连看游戏，两个方块消除的条件不仅仅是图案相同，还得保证两个方块之间的最短连线不能多于两个拐点。你玩连连看，点击两个坐标，游戏是如何判断它俩的最短连线有几个拐点的？

再比如……

```c
// 计算从起点 start 到终点 target 的最近距离
int BFS(Node start, Node target) {
    Queue<Node> q; // 核心数据结构
    Set<Node> visited; // 避免走回头路

    q.offer(start); // 将起点加入队列
    visited.add(start);
    int step = 0; // 记录扩散的步数

    while (q not empty) {
        int sz = q.size();
        /* 将当前队列中的所有节点向四周扩散 */
        for (int i = 0; i < sz; i++) {
            Node cur = q.poll();
            /* 划重点：这里判断是否到达终点 */
            if (cur is target)
                return step;
            /* 将 cur 的相邻节点加入队列 */
            for (Node x : cur.adj())
                if (x not in visited) {
                    q.offer(x);
                    visited.add(x);
                }
        }
        /* 划重点：更新步数在这里 */
        step++;
    }
}
```

**`visited` 的主要作用是防止走回头路，大部分时候都是必须的，但是像一般的二叉树结构，没有子节点到父节点的指针，不会走回头路就不需要 `visited`。**

## 最短路径

给定单板间的有向连接关系：如单板101到单板102存在连接，权值为10，其连接关系描述为：`101 102 10`; 单板102到单板101存在连接，权值为6，其连接关系描述为：`102 101 6`。

基于单板间的有向连接关系，从起始单板到目的单板可能有0或多条可达的有向路径，某条路径权值是该路径上各连接的权值之和。

给定多组格式为`起始单板 目的单板`的查询命令，请依次计算并返回其最佳路径权值；若某条最佳路径不存在，则该条最佳路径权值返回`-1`。

最佳路径的规则如下：

1. 经过的连接个数最少；
2. 若符合规则 1 的路径存在多条，选择权值最小的；

```java
    private static List<Integer> getBestRoute(List<Boardpair> boardsList, List<Connection> connectionsList) {
        // graph[from].add(new int[] {to, weight});每一行是个链表表示graph[from]的相邻元素。
        List<int[]>[] graph = buildGraph(MAX_SIZE, connectionsList);
        List<Integer> res = new ArrayList<>();
        for (Boardpair boardpair : boardsList) {
            //记录访问情况
            boolean[] visited = new boolean[MAX_SIZE];
            //记录初始节点到当前节点的最小连接次数
            int[] distances = new int[MAX_SIZE];
            //记录初始节点到当前节点的最小权重
            int[] weights = new int[MAX_SIZE];
            Arrays.fill(distances, Integer.MAX_VALUE);
            Arrays.fill(weights, Integer.MAX_VALUE);
            int from = boardpair.srcBoard;
            int to = boardpair.snkBoard;
            visited[from] = true;
            distances[from] = 0;
            weights[from] = 0;
            Queue<Integer> queue = new LinkedList<>();
            queue.add(from);
            while (!queue.isEmpty()) {
                int size = queue.size();
                for (int i = 0; i < size; i++) {
                    int cur = queue.poll();
                    for (int[] adj : graph[cur]) {
                        int next = adj[0];
                        int weight = adj[1];
                        int adjDistance = distances[cur] + 1;
                        int adjWeight = weights[cur] + weight;
                        if (adjDistance < distances[next] || (adjDistance == distances[next]
                            && adjWeight <= weights[next])) {
                            distances[next] = adjDistance;
                            weights[next] = adjWeight;
                            if (!visited[next]) {
                                queue.add(next);
                                visited[next] = true;
                            }
                        }
                    }
                }
            }
            res.add(weights[to] == Integer.MAX_VALUE ? -1 : weights[to]);
        }
        return res;
    }
```



## 题目

#### [111.二叉树的最小深度](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree)

给定一个二叉树，找出其最小深度。

最小深度是从根节点到**最近叶子节点**的最短路径上的节点数量。

<img src="https://i0.wp.com/tva2.sinaimg.cn/mw690/00496yplgy1gvo4i21fqvj60qm0ksgq102.jpg" alt="68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303231303230333135353830303530332e706e67">

**求二叉树的最小深度和求二叉树的最大深度的差别主要在于处理左右孩子不为空的逻辑。**

方法二、层序遍历（BFS）

```c
class Solution {
    int minDepth(TreeNode node) {
        if (node == null)
            return 0;
        Deque<TreeNode> deque = new LinkedList<TreeNode>();
        deque.push(node);
        int depth = 1;
        while (!deque.isEmpty()) {
            int size = deque.size();
            for (int i = 0; i < size; i++) {
                TreeNode first = deque.pollFirst();
                if (first.left == null && first.right == null) {
                    System.out.println(first.val);
                    return depth;
                }
                if (first.left != null) {
                    deque.push(first.left);
                }
                if (first.right != null) {
                    deque.push(first.right);
                }
            }
            depth++;
        }
        return -1;
    }
}
```



方法一、因为函数返回子树高度，所以是后序遍历的递归法：推荐

```c
class Solution {
public:
    int minDepth(TreeNode *node)
    {
        if (node == NULL) //空节点
            return 0;
        int leftDepth = minDepth(node->left);    // 左
        int rightDepth = minDepth(node->right);  // 右
                                                 // 中，下面都是中（父亲树）的逻辑
        if (node->left == NULL && node->right == NULL) {
            return 1;
        }
        // 当左子树为空，node最小高度取决于右子树
        if (node->left == NULL) {
            return 1 + rightDepth;
        }

        // 当右子树为空，node最小高度取决于左子树
        if (node->right == NULL) {
            return 1 + leftDepth;
        }
        int result = 1 + min(leftDepth, rightDepth);
        return result;
    }
};
```

#### [752.打开转盘锁](https://leetcode-cn.com/problems/open-the-lock)

![image-20210714152633469](https://cdn.jsdelivr.net/gh/archted/markdown-img@main/img/image-20210714152633469.png)

![image-20210714152646319](https://cdn.jsdelivr.net/gh/archted/markdown-img@main/img/image-20210714152646319.png)



**第一步，我们不管所有的限制条件，不管** **`deadends`** **和** **`target`** **的限制，就思考一个问题：如果让你设计一个算法，穷举所有可能的密码组合，你怎么做**？

穷举呗，再简单一点，如果你只转一下锁，有几种可能？总共有 4 个位置，每个位置可以向上转，也可以向下转，也就是有 8 种可能对吧。

比如说从 `"0000"` 开始，转一次，可以穷举出 `"1000", "9000", "0100", "0900"...` 共 8 种密码。然后，再以这 8 种密码作为基础，对每个密码再转一下，穷举出所有可能...

**仔细想想，这就可以抽象成一幅图，每个节点有 8 个相邻的节点**，又让你求最短距离，这不就是典型的 BFS 嘛，框架就可以派上用场了

```java
int openLock(String[] deadends, String target) {
    // 记录需要跳过的死亡密码
    Set<String> deads = new HashSet<>();
    for (String s : deadends) deads.add(s);
    // 记录已经穷举过的密码，防止走回头路
    Set<String> visited = new HashSet<>();
    Queue<String> q = new LinkedList<>();
    if(deads.contains("0000")){
        return -1;
    }
    
    // 从起点开始启动广度优先搜索
    int step = 0;
    q.offer("0000");
    visited.add("0000");

    while (!q.isEmpty()) {
        int sz = q.size();
        /* 将当前队列中的所有节点向周围扩散 */
        for (int i = 0; i < sz; i++) {
            String cur = q.poll();

            /* 判断是否到达终点 */
            if (deads.contains(cur))
                continue;
            if (cur.equals(target))
                return step;

            /* 将一个节点的未遍历相邻节点加入队列 */
            for (int j = 0; j < 4; j++) {
                String up = plusOne(cur, j);
                if (!visited.contains(up)) {
                    q.offer(up);
                    visited.add(up);
                }
                String down = minusOne(cur, j);
                if (!visited.contains(down)) {
                    q.offer(down);
                    visited.add(down);
                }
            }
        }
        /* 在这里增加步数 */
        step++;
    }
    // 如果穷举完都没找到目标密码，那就是找不到了
    return -1;
}
// 将 s[j] 向上拨动一次
String plusOne(String s, int j) {
    char[] ch = s.toCharArray();
    if (ch[j] == '9')
        ch[j] = '0';
    else
        ch[j] += 1;
    return new String(ch);
}
// 将 s[i] 向下拨动一次
String minusOne(String s, int j) {
    char[] ch = s.toCharArray();
    if (ch[j] == '0')
        ch[j] = '9';
    else
        ch[j] -= 1;
    return new String(ch);
}
```

**进阶、双向BFS**

**传统的 BFS 框架就是从起点开始向四周扩散，遇到终点时停止；而双向 BFS 则是从起点和终点同时开始扩散，当两边有交集的时候停止**。

其实从 Big O 表示法分析算法复杂度的话，它俩的最坏复杂度都是 `O(N)`，但是实际上双向 BFS 确实会快一些，我给你画两张图看一眼就明白了：

![image-20210714160054221](https://cdn.jsdelivr.net/gh/archted/markdown-img@main/img/image-20210714160054221.png)



![image-20210714160107312](https://cdn.jsdelivr.net/gh/archted/markdown-img@main/img/image-20210714160107312.png)

图示中的树形结构，如果终点在最底部，按照传统 BFS 算法的策略，会把整棵树的节点都搜索一遍，最后找到 `target`；而双向 BFS 其实只遍历了半棵树就出现了交集，也就是找到了最短距离。从这个例子可以直观地感受到，双向 BFS 是要比传统 BFS 高效的。

**不过，双向 BFS 也有局限，因为你必须知道终点在哪里**。比如我们刚才讨论的二叉树最小高度的问题，你一开始根本就不知道终点在哪里，也就无法使用双向 BFS；但是第二个密码锁的问题，是可以使用双向 BFS 算法来提高效率的，代码稍加修改即可：

```java
int openLock(String[] deadends, String target) {
    Set<String> deads = new HashSet<>();
    for (String s : deadends) deads.add(s);
    if(deads.contains("0000")){
        return -1;
    }
    // 用集合不用队列，可以快速判断元素是否存在
    Set<String> q1 = new HashSet<>();
    Set<String> q2 = new HashSet<>();
    Set<String> visited = new HashSet<>();

    int step = 0;
    q1.add("0000");
    q2.add(target);

    while (!q1.isEmpty() && !q2.isEmpty()) {
        // 哈希集合在遍历的过程中不能修改，用 temp 存储扩散结果
        Set<String> temp = new HashSet<>();

        /* 将 q1 中的所有节点向周围扩散 */
        for (String cur : q1) {
            /* 判断是否到达终点 */
            if (deads.contains(cur))
                continue;
            if (q2.contains(cur))
                return step;
            visited.add(cur);

            /* 将一个节点的未遍历相邻节点加入集合 */
            for (int j = 0; j < 4; j++) {
                String up = plusOne(cur, j);
                if (!visited.contains(up))
                    temp.add(up);
                String down = minusOne(cur, j);
                if (!visited.contains(down))
                    temp.add(down);
            }
        }
        /* 在这里增加步数 */
        step++;
        // temp 相当于 q1
        // 这里交换 q1 q2，下一轮 while 就是扩散 q2
        q1 = q2;
        q2 = temp;
    }
    return -1;
}
```

加入src：0000，target：9999

第0轮：

q1:0000；

q2:9999；

第一轮后：

q1：9999；

q2:1000/0100/0010/0001

第二轮后(只保存最新一轮扩充的结果，查看q1和q2是否有交集)：

q1：1000/0100/0010/0001

q2：8999/9899/9989/9998

双向 BFS 还是遵循 BFS 算法框架的，只是**不再使用队列，而是使用 HashSet 方便快速判断两个集合是否有交集**。

另外的一个技巧点就是 **while 循环的最后交换** **`q1`** **和** **`q2`** **的内容**，所以只要默认扩散 `q1` 就相当于轮流扩散 `q1` 和 `q2`。

其实双向 BFS 还有一个优化，就是在 while 循环开始时做一个判断：



```c
// ...
while (!q1.isEmpty() && !q2.isEmpty()) {
    if (q1.size() > q2.size()) {
        // 交换 q1 和 q2
        temp = q1;
        q1 = q2;
        q2 = temp;
    }
    // ...
```

为什么这是一个优化呢？

因为按照 BFS 的逻辑，队列（集合）中的元素越多，扩散之后新的队列（集合）中的元素就越多；在双向 BFS 算法中，如果我们每次都选择一个较小的集合进行扩散，那么占用的空间增长速度就会慢一些，效率就会高一些。

不过话说回来，**无论传统 BFS 还是双向 BFS，无论做不做优化，从 Big O 衡量标准来看，时间复杂度都是一样的**，只能说双向 BFS 是一种 trick，算法运行的速度会相对快一点，掌握不掌握其实都无所谓。

