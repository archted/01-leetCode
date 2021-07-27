# **搜索问题**

### FloodFill算法

框架:

```c
// (x, y) 为坐标位置
void fill(int x, int y) {
    fill(x - 1, y); // 上
    fill(x + 1, y); // 下
    fill(x, y - 1); // 左
    fill(x, y + 1); // 右
}
```

**这个框架可以解决所有在二维矩阵中遍历的问题，说得高端一点，这就叫深度优先搜索（Depth First Search，简称 DFS），说得简单一点，这就叫四叉树遍历框架。坐标 (x, y) 就是 root，四个方向就是 root 的四个子节点。**

[733.图像渲染](https://leetcode-cn.com/problems/flood-fill)

<img src="E:\11 个人进步\01 个人总结\LeetCode刷题\assets\leetcode.png" alt="title" style="zoom:80%;" />

用一个和 image 一样大小的二维 bool 数组记录走过的地方，一旦发现重复立即 return。

```c
int[][] floodFill(int[][] image,
        int sr, int sc, int newColor) {

    int origColor = image[sr][sc];
    fill(image, sr, sc, origColor, newColor);
    return image;
}
// 函数意义：若(x,y)坐标颜色为origColor，将(x,y)和周围值为origColor的所有坐标都染色为newColor。
void fill(int[][] image, int x, int y,int origColor, int newColor) {
    if(!inArea()) return ;                //1.先看坐标是否合格
    if(visited[x][y]) return;             //2.再看是否访问过
    if(image[x][y] != origColor) return;  //3.最后看符不符合染色条件、本题中2和3顺序可打乱
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
void fill(int[][] image, int x, int y,
int origColor, int newColor) {
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

### 自动魔棒工具和扫雷

```c
函数意义：若(x,y)坐标颜色为origColor，将(x,y)周围值为origColor的所有坐标的轮廓染色为newColor。
返回值：已经染色或者本轮即将染色的坐标返回1，不符合染色条件的坐标返回0
int fill(int[][] image, int x, int y, int origColor, int newColor) {
    if(!inArea()) return 0;                //1.先看坐标是否合格
    if(visited[x][y]) return 1;            //2.再看是否访问过
    if(image[x][y] != origColor) return 0; //3.最后看符不符合染色条件
    
    visited[x][y] = true;
    
    int surround = 
          fill(image, x - 1, y, origColor, newColor)
        + fill(image, x + 1, y, origColor, newColor)
        + fill(image, x, y - 1, origColor, newColor)
        + fill(image, x, y + 1, origColor, newColor);
    
    if (surround < 4)
        image[x][y] = newColor;
    
    return 1;
}
```

*这个算法有两个细节问题，一是必须借助 visited 来记录已探索的坐标，而无法使用回溯算法；二是开头几个 if 顺序不可打乱。读者可以思考一下原因。*

#### 求岛屿最大面积

695.Max Area of Island (Easy)



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

**`int dfs(vector<vector<int>> &grid, int r, int c)` **

**函数意义：以(r,c)为中心，遍历周围未访问的节点，扩散得到这部分数据形成的岛屿，返回岛屿面积，并使得这部分数据`grid[r][c]=0`，表示已经访问过这部分节点**

​    

```c
class Solution {
   
    int maxAreaOfIsland(vector<vector<int>> &grid)
    {
        if (grid.empty() || grid[0].empty())
            return 0;
        int max_area = 0;
        for (int i = 0; i < grid.size(); ++i) {
            for (int j = 0; j < grid[0].size(); ++j) {
                max_area = max(max_area, dfs(grid, i, j));
            }
        }
        return max_area;
    }
    // 以(r,c)为中心，遍历周围未访问的节点，扩散形成一部分岛屿，返回岛屿面积，并grid[r][c]=0，表示已经访问过该节点
    int dfs(vector<vector<int>> &grid, int r, int c)
    {
        if (r < 0 || r >= grid.size() || c < 0 || c >= grid[0].size()) {
            return 0;
        }
        if (grid[r][c] == 0) { //起到visited数组作用
            return 0;
        }
        grid[r][c] = 0;
        int ret = 1 + dfs(grid, r + 1, c) + dfs(grid, r - 1, c) + dfs(grid, r, c + 1) + dfs(grid, r, c - 1);  //隐式回溯
        return ret;
    }
};
```

#### 求朋友圈数量

547.Friend Circles (Medium)
题目描述
给定一个二维的0-1 矩阵，如果第(i, j) 位置是1，则表示第i 个人和第j 个人是朋友。已知
朋友关系是可以传递的，即如果a 是b 的朋友，b 是c 的朋友，那么a 和c 也是朋友，换言之这
三个人处于同一个朋友圈之内。求一共有多少个朋友圈。
输入输出样例
输入是一个二维数组，输出是一个整数，表示朋友圈数量。因为朋友关系具有对称性，该二
维数组为对称矩阵。同时，因为自己是自己的朋友，对角线上的值全部为1。
Input:
[[1,1,0],
[1,1,0],
[0,0,1]]
Output: 2
在这个样例中，[1,2] 处于一个朋友圈，[3] 处于一个朋友圈。

对于题目695，图的表示方法是，每个位置代表一个节点，每个节点与上下左右四个节点相邻。而在这一道题里面，每一行（列）表示一个节点，它的每列（行）表示是否存在一个相邻节点。因此题目695 拥有`m*n` 个节点，每个节点有4 条边；而本题拥有n 个节点，每个节点最多有n 条边，表示和所有人都是朋友，最少可以有1 条边，表示自己与自己相连.

```c
class Solution {
public:
    
    int findCircleNum(vector<vector<int>> &friends)
    {
        int n = friends.size(), count = 0;
        vector<bool> visited(n, false);
        for (int i = 0; i < n; ++i) {
            if (!visited[i]) {  
                dfs(friends, i, visited);
                ++count;
            }
        }
        return count;
    }
    // 以i为中心，遍历i未访问的朋友，扩散形成一部分朋友圈，使用visited标记已经访问过的朋友
    bool dfs(vector<vector<int>> &friends, int i, vector<bool> &visited)
    {
        visited[i] = true;
        for (int k = 0; k < friends.size(); ++k) {
            if (friends[i][k] == 1 && !visited[k]) {
                cout << "i=" << i << ",k=" << k << endl;
                dfs(friends, k, visited);
            }
        }
        return true;
    }
};
```


#### Pacific Atlantic Water Flow

417. Pacific Atlantic Water Flow (Medium)

题目描述
给定一个二维的非负整数矩阵，每个位置的值表示海拔高度。假设左边和上边是太平洋，右边和下边是大西洋，求从哪些位置向下流水，可以流到太平洋和大西洋。水只能从海拔高的位置流到海拔低或相同的位置。
输入输出样例
输入是一个二维的非负整数数组，表示海拔高度。输出是一个二维的数组，其中第二个维度大小固定为2，表示满足条件的位置坐标。

<img src="E:\11 个人进步\01 个人总结\LeetCode刷题\assets\image-20210524162937362.png" alt="image-20210524162937362" style="zoom:80%;" />

在这个样例中，有括号的区域为满足条件的位置。

虽然题目要求的是满足向下流能到达两个大洋的位置，如果我们对所有的位置进行搜索，那么在不剪枝的情况下复杂度会很高(从坐标(x,y)出发重复算了许多路径）。因此我们可以反过来想，从两个大洋开始向上流，这样我们只需要对矩形四条边进行搜索。搜索完成后，只需遍历一遍矩阵，满足条件的位置即为两个大洋
向上流都能到达的位置。

```c++
class Solution {
public:
    vector<int> direction{-1, 0, 1, 0, -1};
    // 主函数
    vector<vector<int>> pacificAtlantic(vector<vector<int>> &matrix)
    {
        if (matrix.empty() || matrix[0].empty()) {
            return {};
        }
        vector<vector<int>> ans;
        int m = matrix.size(), n = matrix[0].size();
        vector<vector<bool>> can_reach_p(m, vector<bool>(n, false));
        vector<vector<bool>> can_reach_a(m, vector<bool>(n, false));
        for (int i = 0; i < m; ++i) {
            dfs(matrix, can_reach_p, i, 0);
            dfs(matrix, can_reach_a, i, n - 1);
        }
        for (int i = 0; i < n; ++i) {
            dfs(matrix, can_reach_p, 0, i);
            dfs(matrix, can_reach_a, m - 1, i);
        }
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; ++j) {
                if (can_reach_p[i][j] && can_reach_a[i][j]) {
                    ans.push_back(vector<int>{i, j});
                }
            }
        }
        return ans;
    }
    // 以(r,c)为中心，遍历周围未访问的节点，扩散形成一部分岛屿，can_reach[r][c]=0表示已经访问过该节点
    void dfs(const vector<vector<int>> &matrix, vector<vector<bool>> &can_reach, int r, int c)
    {
        if (can_reach[r][c]) {
            return;
        }
        can_reach[r][c] = true;
        int x, y;
        for (int i = 0; i < 4; ++i) {
            x = r + direction[i], y = c + direction[i + 1];
            if (x >= 0 && x < matrix.size() && y >= 0 && y < matrix[0].size() && matrix[r][c] <= matrix[x][y]) {
                dfs(matrix, can_reach, x, y);
            }
        }
    }
};
```

# 