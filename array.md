# array

[TOC]

## LC36.有效的数独

分析题目可以发现该题需要对每一行，每一列，每一个九宫格出现的数字都需要判断是否有重复。所以我们需要哈希表的这种记录结构来判断新元素是否已经在之前出现过。

其次一个容易搞错的点是，我们需要判断九宫格里的元素是否出现过，那就要求我们能根据当前下标范围，拿到九宫格对应范围的哈希结构。

![xx1.png](array/1611905609-HXFmUe-xx1.png)

因为是二维数组，且绝大多的记忆结构都可以用数组来代替，开销比哈希表更少，所以我们用数组充当哈希表记录是否出现过。

```java
boolean[][] rows = new boolean[9][9];
boolean[][] cols = new boolean[9][9];
boolean[][] area = new boolean[9][9];
```

我们用二维数组的从左到右，从上往下的遍历方式遍历每个元素，然后在该过程记录元素是否出现过。

```java
public boolean isValidSudoku(char[][] board) {
    // rows/cols[i][v] 表示第 i 行/列值 v 是否存在
    boolean[][] rows = new boolean[9][9];
    boolean[][] cols = new boolean[9][9];
    // area[i][v] 表示第 i 个区值 v 是否存在
    boolean[][] area = new boolean[9][9];
    for (int i = 0; i < 9; i++) {
        for (int j = 0; j < 9; j++) {
            int c = board[i][j];
            if (c == '.') continue;
            int u = c - '0' - 1;
            int idx = i / 3 * 3 + j / 3;
            if (rows[i][u] || cols[j][u] || area[idx][u]) return false;
            rows[i][u] = col[j][u] = area[idx][u] = true;
        }
    }    
}
```
