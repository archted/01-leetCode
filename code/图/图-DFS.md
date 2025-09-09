## DFS

### 经典模板

1.使用visited标记访问过的下标

```python
visited = set()
def dfs(i, j):
  if i 越界 or j 越界: return   #边界条件，相当于if(root == null) return
  if (i, j) in visited: return
  
  # 标记为访问过
  visited.add((i, j))
  # 上
  dfs(i + 1, j)
  # 下
  dfs(i - 1, j)
  # 右
  dfs(i, j + 1)
  # 左
  dfs(i, j - 1)
  # 撤销标记
  visited.remove((i, j))
# 单点搜索
dfs(0, 0)
# 多点搜索
for i in range(M):
   for j in range(N):
      dfs(i, j)
```

2.直接原地标记

```python
def dfs(i, j):
  if i 越界 or j 越界: return
  if board[i][j] == -1: return
  temp = board[i][j]
  # 标记为访问过
  board[i][j] = -1
  # 上
  dfs(i + 1, j)
  # 下
  dfs(i - 1, j)
  # 右
  dfs(i, j + 1)
  # 左
  dfs(i, j - 1)
  # 撤销标记
  board[i][j] = temp
# 单点搜索
dfs(0, 0)
# 多点搜索
for i in range(M):
   for j in range(N):
      dfs(i, j)
```

上面框架是先污染后治理—— 甭管当前是在哪个格子，先往四个方向走一步再说，如果发现走出了网格范围再赶紧返回。，我们看一下先治理后污染的框架：

3.**先治理后污染,当dfs需要返回值时，这种写法更方便理解哪种条件下需要什么样的返回值。**

```python
def dfs(i, j):
  temp = board[i][j] # 走到这里的(i,j)必然满足不越界且没有访问过
  # 标记为访问过
  board[i][j] = -1
  if ((i+1,j)不越界 && !visited[i+1][j]):
  	dfs(i + 1, j)
  if ((i-1,j)不越界 && !visited[i-1][j]):
    dfs(i - 1, j)
  if ((i,j+1)不越界 && !visited[i][j+1]):
    dfs(i, j + 1)
  if ((i,j-1)不越界 && !visited[i][j-1]):
    dfs(i, j - 1)
  # 撤销标记
  board[i][j] = temp
# 单点搜索
dfs(0, 0)
# 多点搜索
for i in range(M):
   for j in range(N):
      if(!visited([i][j])) dfs(i, j);
```



**这个框架可以解决所有在二维矩阵中遍历的问题，说得高端一点，这就叫深度优先搜索（Depth First Search，简称 DFS），说得简单一点，这就叫四叉树遍历框架。坐标 (x, y) 就是 root，四个方向就是 root 的四个子节点。**使用visited的原因是防止节点被重复访问，而二叉树遍历的时候左右孩子指针自带方向，不用担心节点被重复访问。



### FloodFill算法 [733\. 图像渲染](https://leetcode-cn.com/problems/flood-fill/)

Difficulty: **简单**


有一幅以二维整数数组表示的图画，每一个整数表示该图画的像素值大小，数值在 0 到 65535 之间。

给你一个坐标 `(sr, sc)` 表示图像渲染开始的像素值（行 ，列）和一个新的颜色值 `newColor`，让你重新上色这幅图像。

为了完成上色工作，从初始坐标开始，记录初始坐标的上下左右四个方向上像素值与初始坐标相同的相连像素点，接着再记录这四个方向上符合条件的像素点与他们对应四个方向上像素值与初始坐标相同的相连像素点，……，重复该过程。将所有有记录的像素点的颜色值改为新的颜色值。

最后返回经过上色渲染后的图像。

**示例 1:**

```
输入: 
image = [[1,1,1],[1,1,0],[1,0,1]]
sr = 1, sc = 1, newColor = 2
输出: [[2,2,2],[2,2,0],[2,0,1]]
解析: 
在图像的正中间，(坐标(sr,sc)=(1,1)),
在路径上所有符合条件的像素点的颜色都被更改成2。
注意，右下角的像素没有更改为2，
因为它不是在上下左右四个方向上与初始点相连的像素点。
```

**注意:**

*   `image` 和 `image[0]` 的长度在范围 `[1, 50]` 内。
*   给出的初始点将满足 `0 <= sr < image.length` 和 `0 <= sc < image[0].length`。
*   `image[i][j]` 和 `newColor` 表示的颜色值在范围 `[0, 65535]`内。



用一个和 image 一样大小的二维 bool 数组记录走过的地方，一旦发现重复立即 return。

```c
int[][] floodFill(int[][] image,int sr, int sc, int newColor) {

    int origColor = image[sr][sc];
    fill(image, sr, sc, origColor, newColor);
    return image;
}
// 函数意义：若(x,y)坐标颜色为origColor，将(x,y)和周围值为origColor的所有坐标都染色为newColor。
void fill(int[][] image, int x, int y,int origColor, int newColor) {
    if(!inArea(image,x,y)) return ;                //1.先看坐标是否合格
    if(visited[x][y]) return;             //看是否访问过
    if(image[x][y] != origColor) return;  //看符不符合染色条件
    visited[x][y] = true;
    image[x][y] = newColor;
    
    fill(image, x, y + 1, origColor, newColor);
    fill(image, x, y - 1, origColor, newColor);
    fill(image, x - 1, y, origColor, newColor);
    fill(image, x + 1, y, origColor, newColor);
}

boolean inArea(int[][] image, int x, int y) {
    return x >= 0 && x < image.length && y >= 0 && y < image[0].length;
}
```

回溯思想：

```c
void fill(int[][] image, int x, int y,int origColor, int newColor) {
    // 出界：超出数组边界
    if (!inArea(image, x, y)) return;
    // 碰壁：遇到其他颜色，超出 origColor 区域
    if (image[x][y] != origColor) return;
    // 已探索过的 origColor 区域
    if (image[x][y] == -1) return;
    
    // choose：打标记，以免重复
    image[x][y] = -1;
    fill(image, x, y + 1, origColor, newColor);
    fill(image, x, y - 1, origColor, newColor);
    fill(image, x - 1, y, origColor, newColor);
    fill(image, x + 1, y, origColor, newColor);
    // unchoose：将标记替换为 newColor
    image[x][y] = newColor;
}
```

### 岛屿问题

岛屿问题是一类经典的网格搜索类问题。以[463. 岛屿的周长](https://leetcode-cn.com/problems/island-perimeter/)为例讲解这类题型，我们首先来看如何在网格上做 DFS，再看如何在 DFS 的时候求岛屿的周长。

#### [463. 岛屿的周长](https://leetcode-cn.com/problems/island-perimeter/)

![image-20210811093456059](https://i0.wp.com/tvax2.sinaimg.cn/large/00496yplgy1gvnwomdzysj60l70lk44c02.jpg)

![image-20210811093506514](https://i0.wp.com/tvax4.sinaimg.cn/large/00496yplgy1gvnwom7uhtj60l903dmx902.jpg)

一、如何在网格上做DFS

1.处理边界(终止条件)

从二叉树的 base case 对应过来，网格 DFS 中的 base case应该是那些超出网格范围的格子。

![网格 DFS 的 base case](https://pic.leetcode-cn.com/5a91ec351bcbe8e631e7e3e44e062794d6e53af95f6a5c778de369365b9d994e.jpg)

这一点稍微有些反直觉，坐标竟然可以临时超出网格的范围？这种方法我称为**「先污染后治理」**—— 甭管当前是在哪个格子，先往四个方向走一步再说，如果发现走出了网格范围再赶紧返回。这跟二叉树的遍历方法是一样的，先递归调用，发现 root == null 再返回。还有一种方法是「先治理后污染」，即先判断坐标是否超出网格范围，若坐标合法再进行DFS。

2.DFS 可能会不停地“兜圈子”，需要使用visited或者改变方格中存储的值表示已经被访问过了。

这样，我们就得到了网格 DFS 遍历的框架代码：

```java
// 标记已遍历过的岛屿，不做重复遍历
void dfs(int[][] grid, int r, int c) {
    if (!(0 <= r && r < grid.length && 0 <= c && c < grid[0].length)) {
        return;
    }
    // 等价于if( grid[r][c] == 0 || grid[r][c] == 2) return;
    if (grid[r][c] != 1) {
        return;
    }
    grid[r][c] = 2; // 将方格标记为"已遍历"
    dfs(grid, r - 1, c); 
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}
```

二、如何在 DFS 遍历时求岛屿的周长

求岛屿的周长其实有很多种方法，如果用 DFS 遍历来求的话，有一种很简单的思路：岛屿的周长就是岛屿方格和非岛屿方格相邻的边的数量。注意，这里的非岛屿方格，既包括水域方格，也包括网格的边界。我们可以画一张图，看得更清晰：

![岛屿周长的两类划分](https://pic.leetcode-cn.com/e0e2314bb62cb06383e6128a6ba2b75e7c942cc5a36dedc32d0b39868a597629.jpg)

因此，DFS函数含义为：每当在 DFS 遍历中，从一个岛屿方格走向一个非岛屿方格，就将周长加 1。代码如下：

```java
    int dfs(int[][] grid, int x, int y) {
        // 从一个岛屿方格走向网格边界，周长加 1
        if (!(0 <= x && x < grid.length && 0 <= y && y < grid[0].length)) {
            return 1;
        }
        // 从一个岛屿方格走向水域方格，周长加 1
        if (grid[x][y] == 0) {
            return 1;
        }
        if (grid[x][y] == 2) { // 已遍历过
            return 0;
        }
        grid[x][y] = 2;
        return dfs(grid, x - 1, y) + dfs(grid, x + 1, y) + dfs(grid, x, y - 1) + dfs(grid, x, y + 1);
    }
```

如果上面代码条件的返回值不好理解，可以转换为先治理后污染的框架(类比于二叉树遍历过程中先对node判空，再递归)：

```java
    int dfs2(int[][] grid, int x, int y) {
        int res = 0;
        int[][] offs = { { 0, 1 }, { 0, -1 }, { 1, 0 }, { -1, 0 } };
        for (int[] off : offs) {
            int i = x + off[0];
            int j = y + off[1];
            if (!(i >= 0 && i < grid.length && j >= 0 && j < grid[0].length) ) {
                res += 1;    // 从一个岛屿方格走向网格边界，周长加 1
            } else if (grid[i][j] == 0) {
                res += 1;    // 从一个岛屿方格走向水域方格，周长加 1
            } else if (grid[i][j] == 2) {
                res += 0;	// 已遍历过
            } else {
                grid[i][j] = 2;
                res += dfs2(grid, i, j);
            }
        }
        return res;
    }
```

三、最终，我们得到完整的题解代码：

```java
public int islandPerimeter(int[][] grid) {
    for (int r = 0; r < grid.length; r++) {
        for (int c = 0; c < grid[0].length; c++) {
            if (grid[r][c] == 1) {
                // 题目限制只有一个岛屿，计算一个即可
                return dfs(grid, r, c);
            }
        }
    }
    return 0;
}
    int dfs(int[][] grid, int x, int y) {
        // 从一个岛屿方格走向网格边界，周长加 1
        if (!(0 <= x && x < grid.length && 0 <= y && y < grid[0].length)) {
            return 1;
        }
        // 从一个岛屿方格走向水域方格，周长加 1
        if (grid[x][y] == 0) {
            return 1;
        }
        if (grid[x][y] == 2) { // 已遍历过
            return 0;
        }
        grid[x][y] = 2;
        return dfs(grid, x - 1, y) + dfs(grid, x + 1, y) + dfs(grid, x, y - 1) + dfs(grid, x, y + 1);
    }
```

当然也可以不使用DFS：

解法二、岛屿的周长等价于求每个陆地不与其他陆地相邻的边数；

```java
class Solution {
    public int islandPerimeter(int[][] grid) {
        int row = grid.length;
        int col = grid[0].length;
        int res = 0;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (grid[i][j] == 1) {
                    res += countLine(grid, i, j);
                }
            }
        }
        return res;
    }

    int countLine(int[][] grid, int x, int y) {
        int line = 0;
        int[][] offs = { { 0, 1 }, { 0, -1 }, { 1, 0 }, { -1, 0 } };
        for (int[] off : offs) {
            int i = x + off[0];
            int j = y + off[1];
            int add = ((i >= 0 && i < grid.length && j >= 0 && j < grid[0].length) && grid[i][j] == 1) ? 0 : 1;
            line += add;
        }
        return line;
    }
}
```





#### [695. 岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/)

题目描述
给定一个二维的0-1 矩阵，其中0 表示海洋，1 表示陆地。单独的或相邻的陆地可以形成岛屿，每个格子只与其上下左右四个格子相邻。求最大的岛屿面积。

输入输出样例
输入是一个二维数组，输出是一个整数，表示最大的岛屿面积。
Input:
[[1,0,1,1,0,1,0,1],
[1,0,1,1,0,1,1,1],
[0,0,0,0,0,0,0,1]]
Output: 6
最大的岛屿面积为6，位于最右侧。

---

题意分析：遍历二维矩阵，每遇到一个岛屿，便计算岛屿的面积，最后求所有岛屿的最大面积。   

```c
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        int row = grid.length;
        int col = grid[0].length;
        int res = 0;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (grid[i][j] == 1) {//遇到一个岛屿
                    res = Math.max(res, dfs(grid, i, j));
                }
            }
        }
        return res;
    }

    // 统计(x,y)形成的岛屿的面积,并将遍历过的节点染色成2；
    int dfs(int[][] grid, int x, int y) {
        int row = grid.length;
        int col = grid[0].length;
        if (!(x >= 0 && x < row && y >= 0 && y < col)) {
            return 0;
        }
        if (grid[x][y] != 1) {
            return 0;
        }
        grid[x][y] = 2;
        int res = 1;
        int[][] offs = { { 0, 1 }, { 0, -1 }, { 1, 0 }, { -1, 0 } };
        for (int[] off : offs) {
            int i = x + off[0];
            int j = y + off[1];
            res += dfs(grid, i, j);
        }
        return res;
    }
}
```

#### [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

在[695. 岛屿的最大面积]答案稍微改一下，就可以AC这道题了。

```java
class Solution {
    public int numIslands(int[][] grid) {
        int row = grid.length;
        int col = grid[0].length;
        int res = 0;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if (grid[i][j] == 1) {// 遇到一个新的岛屿
                    dfs(grid, i, j);
                    res++;
                }
            }
        }
        return res;
    }

    // 将(x,y)形成的岛屿所有节点染色成2；
    void dfs(int[][] grid, int x, int y) {
        int row = grid.length;
        int col = grid[0].length;
        if (!(x >= 0 && x < row && y >= 0 && y < col)) {
            return;
        }
        if (grid[x][y] != 1) {
            return;
        }
        grid[x][y] = 2;
        int[][] offs = { { 0, 1 }, { 0, -1 }, { 1, 0 }, { -1, 0 } };
        for (int[] off : offs) {
            int i = x + off[0];
            int j = y + off[1];
            dfs(grid, i, j);
        }
    }
}
```

二、并查集

关于连通性问题，并查集也是常用的数据结构。

思路：

并查集中维护连通分量的个数，在遍历的过程中：

相邻的陆地（只需要向右看和向下看）合并，只要发生过合并，岛屿的数量就减少 1；
在遍历的过程中，同时记录空地的数量；
并查集中连通分量的个数 - 空地的个数，就是岛屿数量。

