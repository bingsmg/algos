# dp

reference link:

MIT：https://youtu.be/OQ5jsbhAv_M?si=JiwQsNpbsaIkuhIm

动态规划 = 递归 + 记录状态

解决问题结果由很多重叠子问题的结果推导而来的问题。所以一般应用于**最优子问题**结构，递推出最优结果。与搜索算法不同的地方在于记录子问题的结果避免重复搜索。

该系列题目最重要的是对于 dp 数组的定义，以及 dp 数组的推导过程。因为有些题目是直接定义题目结果为 dp 数组，而有些题目需要先定义并计算 dp 数组，并从 dp 数组计算推导出最终结果。

一般题目输入为一维数组和二维数组，我们需要推导  dp 数组，联系数组，其实大多情况下，我们的定义针对 dp[i] 来思考，一般定义为以 dp[i] 结尾的...巨多，因为这样遍历完数组，就能推导出答案。

针对 dp 数组的长度为原输入数组的 n 还是 n + 1，其实主要看你定义的时候如何定义，如果长度为 n，则 dp 的定义很多时候并不是符合我们思考的规律，因为我们的思维是有一个元素算是第一个，所以一般是 **"第一个~第 n 个"**，但是对于数组来说通过下标访问的时候却是 nums[0]-nums[n - 1] 所以，不同的定义在每一个元素的处理上要保证数组下标不越界，保证逻辑定义和数组中处理的元素一致即可。**所以定义为 n + 1 长度是为了更加符合我们的直觉和思考方式。**

## 经典入门

### 70.爬楼梯

[70. 爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

可以把输入建模为 1...n 的一维数组，结果要求到达 n 的所有不同方式。

**dp[i] 表示爬上第 i 层台阶有多少不同方式**

所以 dp[i] = dp[i - 1] + dp[i - 2]

记录状态的递归：

```java
class Solution {
    public int climbStairs(int n) {
        Map<Integer, Integer> memo = new HashMap<>();
        return dfs(n, memo);
    }

    public int dfs(int n, Map<Integer, Integer> memo) {
        if (n == 1) return 1;
        if (n == 2) return 2;
        
        if (memo.containsKey(n)) return memo.get(n);
        else memo.put(n, dfs(n-1, memo) + dfs(n-2, memo));
   
        return memo.get(n);
    }
}
```

转化为 dp：

```java
class Solution {
    public int climbStairs(int n) {
        if (n <= 1) return n;
        int[] dp = new int[n + 1];
        dp[1] = 1;
        dp[2] = 2;
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
}
```

### 509.斐波那契数

[509. 斐波那契数](https://leetcode.cn/problems/fibonacci-number/)

题目直接告诉你递推关系：`F(n) = F(n - 1) + F(n - 2)，其中 n > 1`

这道题目引出了空间压缩的概念，即当你发现 dp[i] 只与 dp[i - 1] 有关，且之后不会再用到，就可以用常量代替。

```java
class Solution {
    public int fib(int n) {
        int ans = -1;
        if (n == 1 || n == 0) return n;
        int pre = 0, cur = 1;
        for (int i = 2; i <= n; i++) {
            ans = pre + cur;
            pre = cur;
            cur = ans;
        }
        return ans;
    }
}
```

### 413.等差数列划分

[413. 等差数列划分](https://leetcode.cn/problems/arithmetic-slices/)

本题目 dp[i] 的定义为 "**以 nums[i] 结尾的连续子数组的最大个数**"

所以要求最总结果，就需要将以每个元素结尾的 dp[0->n-1] 累加从而得到能划分的所有等差数列。

```java
class Solution {
    public int numberOfArithmeticSlices(int[] nums) {
        int n = nums.length;
        int ans = 0;
        if (n < 3) return ans;
        int[] dp = new int[nums.length];
        for (int i = 2; i < n; i++) {
            if (nums[i] - nums[i - 1] == nums[i - 1] - nums[i - 2]) {
                dp[i] = dp[i - 1] + 1;
                ans += dp[i];
            }
        }
        return ans;
    }
}
```

## 股票



## 背包



## 子数组乘积

子数组乘积是，针对子数组里的元素，有正有负，所以你选择一个正数或负数造成的影响是不一样的。我们一般题目要求求最值，最值一般都可以用到 DP 的思维，从数组开始遍历到结束，在这个过程中维护想要的结果进行更新，针对某个元素进行选取与不选取的不同处理。

### LC152.乘积最大的子数组

[152. 乘积最大子数组](https://leetcode.cn/problems/maximum-product-subarray/)



### LC2708.一个小组最大的实力值

[2708. 一个小组的最大实力值](https://leetcode.cn/problems/maximum-strength-of-a-group/)

