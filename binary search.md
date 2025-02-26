# binary search

[toc]

reference link:

[34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

[300. 最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

二分，是在一个范围内，查找某个元素，这个范围内的元素必须满足单调性，基于单调性质，可以通过二分来做，让时间变为 logn。

二分不同的题目有不同的写法，总之在于你要找的目标位置的范围是什么样的：

- 数组的长度有没有可能是问题答案，即 n 有没有可能是问题答案，必须讨论。
- mid 和 target 的讨论，即下一次查找区间，mid 能否作为左右边界来考虑，查找 target 第一次出现，则当 mid == target 时，hi = mid，最后一次出现则 lo = mid。其次思考清楚当 mid > target 或者 mid < target 的时候，哪一个区间是下一次不可能搜索的。
- while (lo < hi) 和 while (lo <= hi) 什么时候采用什么写法呢，其实问题在于，返回的结果会是 lo+1 还是 lo，因为前者返回 lo == hi，后者返回 lo > hi。
- 有一种死循环的原因是 lo = mid，然后例如当 lo = 3, hi = 4 时候，每次 mid = 3，所以导致 lo 每次等于 mid，最后引起死循环错误。\

有一种教学说法是严格围绕你对区间的定义来缩减 lo 和 hi，区间是开区间还是闭区间影响最后 lo 和 hi 的缩减，不知道这种有没有说法，我对此并不能完全理解这种记忆思考方式。

## LC35.搜索插入位置

[35. 搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

如果 nums 存在 target 返回对应数组下标，如果不存在返回需要插入的位置。

对于二分搜索，最让人不知道如何选择的第一个是对于搜索区间的左闭右闭还是左闭右开。其实我们思考的重点在于最终答案是 0->n 还是 0->n-1。即首先考虑极限情况，会返回什么。

对于本题来说，如果 target 小于 nums 全部元素，返回 0，大于全部元素，返回 n，所以我们的搜索区间直接定义为 0->n，但是为了防止当 mid 为 n 的时候数组下标越界，我们在 while 里写 (lo<hi)。那里面的逻辑呢？当 mid 小于 target 我们知道插入的位置一定在该位置之后，所以下次的 mid 为 lo + 1，当 mid 大于 target，我们知道 mid 可能就是需要插入的位置，所以下次 hi = mid。

重点是一定要分析好边界情况，代码如下：

```java
public int bs(int[] nums, int target) { // binary search
    int n = nums.length;
    int lo = 0, hi = n;
    while (lo < hi) {
        int mid = lo + (hi - lo) / 2;
        if (nums[mid] > target) hi = mid;
        else if (nums[mid] == target) return mid;
        else lo = mid + 1;
    }
    return lo;
}
```

## LC34.在排序数组中查找元素第一个和最后一个位置

根据灵神视频所说，二分查找使用了 红蓝染色法，即牢记自己的区间定义。找到 mid 之后，**根据单调性，用红色表示左半部分，表示 false，蓝色表示右半部分，表示 true，**

<img src="binary search/image-20240930115124140.png" alt="image-20240930115124140" style="zoom:50%;" />  

## LC300.最长递增子序列

给一个整数数组 nums，让你找出最长的严格递增子序列长度。

二分查找的思想其实基于贪心策略，即我们用数组 tails[len] 来表示长度为 len 的末尾元素最小的子序列。那么我们在遍历过程中的处理就是如果 nums[i] > tails[len] 则 tails[++len] = nums[i]。如果发现 nums[i] < tails[len]，我们就需要去找到该元素在长度为 len 时插入序列的最小位置，此处我们可以用二分解决。二分法的查找逻辑即：在数组中查找 target 插入的位置。如果 nums[mid] <= target 则说明 mid 的位置用红色标记发现 mid 及左边元素全部不满足，如果发信啊 nums[mid] >= target 则说明 mid 可能是插入的位置。

```java
public int lengthOfLIS(int[] nums) {
        int n = nums.length;
        // tail[len] 表示长度为 len 的末尾元素最小的，最长递增子序列
        int[] tail = new int[n + 1]; // 最长为 n
        int len = 1;
        tail[len] = nums[0];
        for (int i = 1; i < n; i++) {
            if (nums[i] > tail[len]) tail[++len] = nums[i];
            else {
                int lo = 1, hi = len;
                while (lo < hi) {
                    int mid = lo + (hi - lo) / 2;
                    if (tail[mid] < nums[i]) lo = mid + 1;
                    else hi = mid;
                }
                tail[lo] = nums[i];
            }
        }
        return len;

    }
```

