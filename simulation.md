# simulation

[toc]

模拟类的题目，主要是要根据题目的描述和求解经过，将题目所给的输入按照描述的过程演算一遍，在这个过程中去记录或者处理最终结果。

## LC14222.分割字符串的最大得分

[1422. 分割字符串的最大得分](https://leetcode.cn/problems/maximum-score-after-splitting-a-string/)

分割一个位字符串为两部分，左串 0 得分，右串 1 得分，求最大得分。例如 "01001" 分为 "0100" 和 "1"，左串 3 个 0 得 3 分，右串 1 个 1 得一分，最大得 4 分。

原始的思路即迭代每一个分割点，判断左右两侧的串中 0 和 1 的分别个数，累计结果，保存最大结果。O(n^2) 的时间复杂度。

有没有更简单的思路呢？我们在遍历过程中，要判断所有分割情况带来的最大得分，我们优先记录两个串的最大得分，两个串分别为 "0" 和 "1001" 的最大得分 score。

之后遍历下标从 1->n-1 判断当前的字符是 0 还是 1，如果是 0，会增加左串的得分，不改变右串得分，即总得分 + 1；如果是 1，说明左串多了一个不加分的位，但是右串的得分降低了，所以我们的 score 会 - 1，在这个过程中记录最大的得分即可得到最终结果，需要注意的是我们必须分为两个非空串，为了防止 "00" 这种串，我们的二次遍历索引 < n - 1。

```java
public int maxScore(String s) {
    char cs = s.toCharArray();
    int n = cs.length;
    int score = 0;
    if (cs[0] == '0') score++;
    int ans = score;
    for (int i = 1; i < n; i++) {
        if (cs[i] == '1') score++;
    }
    for (int i = 1; i < n - 1; i++) { // !!! i < n-1
        if (cs[i] == '0') score++;
        else score--;
        ans = Math.max(ans, score);
    }
    return ans;
}
```

## LC1041.困于环中的机器人

[1041. 困于环中的机器人](https://leetcode.cn/problems/robot-bounded-in-circle/)

要判断机器人在给定指令后能否陷入循环，机器人的行进方向有只有 go straight 可以转向 left 和 right，通过 left 和 right 可以让机器人沿着 xy 二维坐标系上下左右移动，最终会不会陷入循环我们这么思考。假定机器人原来的坐标为 (x, y)

- 如果机器人仍然朝北，那么机器人可以不会陷入循环。假设执行完一串指令后，机器人的位置是 (x,y) 且不为原点，方向仍然朝北，那么执行完第二串指令后，机器人的位置便成为 (2×x,2×y)，会不停地往外部移动，不会陷入循环。
- 如果机器人朝南，那么执行第二串指令时，机器人的位移会与第一次相反，即第二次的位移是 (−x,−y)，并且结束后会回到原来的方向。这样一来，每两串指令之后，机器人都会回到原点，并且方向朝北，机器人会陷入循环。
- 如果机器人朝东，即右转了 90°。这样一来，每执行一串指令，机器人都会右转 90°。那么第一次和第三次指令的方向是相反的，第二次和第四次指令的方向是相反的，位移之和也为 0，这样一来，每四次指令之后，机器人都会回到原点，并且方向朝北，机器人会陷入循环。如果机器人朝西，也是一样的结果。

```java
public boolean isRobotBounded(String instructions) {
    int[][] dir = new int[][]{{0, 1}, {1, 0}, {0, -1}, {-1, 0}}; // 对于东南西北二维坐标系坐标变化
    int x = 0, y = 0;
    int dirIdx = 0;
    int n = instructions.length();
    for (int i = 0; i < n; i++) {
        char c = instructions.charAt(i);
        if (c == 'G') {
            x += dir[dirIdx][0];
            y += dir[dirIdx][1];
        } else if (c == 'L') {
            dirIdx += 3;
            dirIdx %= 4;
        } else {
            dirIdx++;
            dirIdx %= 4;
        }
    }
    return dirIdx != 0 || (x == 0 && y == 0);
}
```

