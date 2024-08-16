# dfs 专题总结

深度优先搜索 dfs和广度优先搜索 bfs是最常见的两种搜索方法，常见的搜索基于树和图，而图的表现形式以数组居多，以下以 dfs 和 bfs 描述。

dfs 在搜索到一个新的节点时，立即对该节点进行遍历，dfs 用栈或者递归实现，递归居多。搜索的过程中根据不同的题目类型我们需要对已经搜索过的节点进行记录，防止重复搜索到某个节点，这种做法叫**状态记录**或者**记忆化**。

## LC695.岛屿的最大面积

[695. 岛屿的最大面积](https://leetcode.cn/problems/max-area-of-island/)<img src="https://assets.leetcode.com/uploads/2021/05/01/maxarea1-grid.jpg" alt="img" style="zoom:50%;" />

### 题目分析

> 其实很多题目如果以图的形式表现，很容易发现解题规律，但大多必须通过文字在大脑中模拟出图形，这个过程其实不容易。

问岛屿的最大面积，即问二维数组中连续 1 的最大子区域的面积，我们把每一个点的面积记作 1。所以我们需要遍历所有的这样的块，并记录最大值。

针对每一个点，我们可以向它的周围发散，上右下左四个方向进行，每一个方向的搜索和该节点的搜索逻辑是一样的，符合 dfs 的思维。

如何向四周发散呢？有两种实现，如下：

```java
private int dfs(int[][] grid, int i, int j) {
    // m 和 n 为矩阵的长和宽
    if (i < 0 || i >= m || j < 0 || j >= n) return 0;
    if (grid[i][j] == 0) return 0;
    // 记录遍历完状态
    grid[i][j] = 0;
    // 以该点出发的子区域总面积为：该点+上右下左区域面积
    return 1 + dfs(grid, i - 1, j) + dfs(grid, i, j + 1) + dfs(grid, i + 1, j) + dfs(grid, i, j - 1);
}
```

或者：

```java
// directions = {-1, 0, 1, 0, -1};
private int dfs(int[][] grid, int i, int j) {
    grid[i][j] = 0;
    int area = 1;
    for (int k = 0; k < 4; k++) {
        int x = i + directions[k], y = j + directions[k + 1];
        if (x < 0 || x >= m || y < 0 || y >= n) continue;
        if (grid[x][y] == 0) continue;
        area += dfs(grid, x, y);
    }
    return area;
}
```

### 题解

```java
public int maxAreaOfIsland(int[][] grid) {
    int ans = 0;
    int m = grid.length;
    int n = (m == 0 ? 0 : grid[0].length);
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 1) {
                ans = Math.max(ans, dfs(grid, i, j));
            }
        }
    }
    return ans;
}
```

## LC547.朋友圈/省份数量

[547. 省份数量](https://leetcode.cn/problems/number-of-provinces/)

### 题意分析

有 n 个市，如果市与市有关联，关联关系通过二维数组给出，则它们属于同一个省份，问一共有多少个省。我们知道城市 a 和 b 关联，在二位数数组中的表现就是 i 行 j 列及 j 行 i 列都是 1，所以其实我们遍历 n 个市即可，在遍历一个市的时候，我们搜索所有和它关联的其他市，然后记录搜索状态，从而得出所有省份。

dfs 针对每个省份的搜索，我们用 visited 数组来记录状态。如下：

> 二维数组默认用 m 表示长，n 表示宽

```java
private void dfs(int[][] isConnected, int i) {
    visited[i] = true;
    for (int j = 0; j < m; j++) {
        if (isConnected[i][j] == 1 && !visited[j]) {
            dfs(isConnected, j);
        }
    }
}
```

### 题解

```java
public int findCircleNum(int[][] isConnected) {
    int ans = 0;
    int m = isConnected.length;
    boolean[] visited = new boolean[m];
    for (int i = 0; i < m; i++) {
        if (visited[i]) {
            dfs(isConnected, i);
            ans++;
        }
    }
    return ans;
}
```
