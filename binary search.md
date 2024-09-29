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
- 有一种死循环的原因是 lo = mid，然后例如当 lo = 3, hi = 4 时候，每次 mid = 3，所以导致 lo 每次等于 mid，最后引起死循环错误。

