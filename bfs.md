# bfs

[toc]

广度优先搜索一般使用队列实现，大多用来求有关层级和路径的问题，因为队列的一次循环，处理一层的元素，然后在这一层的元素处理中，会添加下一层的待处理元素。假设队列已经添加了待处理元素点的起始层：

```java
while (!queue.isEmpty()) {
    level += 1; // level 从 0 开始
    int size = queue.size();
    while (size-- > 0) {
        e = queue.pop(); // element
        // ...process(e) -> queue.push(newElement) 处理当前层元素时添加下一层待处理元素
    }
    
}
```

一般遍历需要在处理过程中防止重复递归，所以需要有状态记录的操作，标识是否已经处理过元素，一般在原数组或者原结构里设置一个新的标识去判断，或者新建一个标识数组 visited。

## LC934.最短的桥

[934. 最短的桥](https://leetcode.cn/problems/shortest-bridge/)

题目已经限定一定会有两个岛屿，那么我们其实就可以先去找到第一个岛屿，然后将第一个岛屿相邻的所有水域坐标加入队列，然后在访问岛屿的过程中，设置原来为 1 的岛屿表示为 2，标识已经访问处理了。

之后我们遍历队列，每一次将当前水域处理为 2，并将它相邻的水域添加了队列，从而就能通过待处理元素的层级来判断岛屿之间的距离，我们在处理水域的过程中，如果发现水域相邻的坐标是 1，即未访问过的第二个岛屿，则我哦们得到了答案。此处处理访问过的元素，我们修改原数组的值为 2 来标识。从而避免创建新数组。

```java
class Solution {
    
    private static final int[] directions = {-1, 0, 1, 0, -1};
    private static final Deque<int[]> queue = new ArrayDeque<>();

    public int shortestBridge(int[][] grid) {
        queue.clear();
        int m = grid.length, n = (m == 0 ? 0 : grid[0].length);
        boolean flag = false;
        for (int i = 0; i < m; i++) {
            if (flag) break;
            for (int j = 0; j < n; j++) {
                if (!flag && grid[i][j] == 1) { // 访问第一个岛屿，并处理所有节点为 2
                    dfs(grid, i, j, m, n);
                    flag = true;
                }
            }
        }

        int level = 0;
        while (!queue.isEmpty()) {
            level++;
            int size = queue.size();
            while (size-- > 0) {
                int[] point = queue.pollFirst();
                for (int i = 0; i < 4; i++) {
                    int x = point[0] + directions[i];
                    int y = point[1] + directions[i + 1];
                    if (x < 0 || x == m || y < 0 || y == m || grid[x][y] == 2) continue;
                    if (grid[x][y] == 1) { // 第二个岛屿则直接返回
                        return level;
                    }
                    queue.offerLast(new int[]{x, y});
                    grid[x][y] = 2;
                }
            }
        }
        return level;
    }

    private void dfs(int[][] grid, int i, int j, int m, int n) {
        if (i < 0 || i == m || j < 0 || j == n || grid[i][j] == 2) return;
        if (grid[i][j] == 0) {
            // 添加第一个岛屿相邻的所有水域，未后来 bfs 做准备
            queue.offerLast(new int[]{i, j});
            return;
        }
        grid[i][j] = 2;
        // 上右下左
        dfs(grid, i - 1, j, m, n);
        dfs(grid, i, j + 1, m, n);
        dfs(grid, i + 1, j, m, n);
        dfs(grid, i, j - 1, m, n);
    }
}
```
