# hash table

[toc]

## LC49.字母异位词分组

[49. 字母异位词分组](https://leetcode.cn/problems/group-anagrams/)

给定一个字符串数组，要将字母异位词组合到一起，然后返回结果数组，字母异位词就是重新排列原单词所有字母能得到的所有新单词。

从字母异位词的描述我们就能知道同一对字母异位词的单词，按照字母排序后肯定是一样的，那么我们就很明显的想到和哈希表之间的联系。用同一组字母异位词排序后的新的统一单词作为 key，然后将这一组所有的字母异位词组成的列表作为 value，就实现了该需求，因为哈希表的 key 是唯一的，所以也保证了在这个过程中只需要遍历一次字符串数组即可。

```java
public List<List<String>> groupAnagrams(String[] strs) {
    Map<String, List<String>> ht = new HashMap<>(); // ht-hashtable 简写
    for (String str : strs) {
        char[] cs = str.toCharArray();
        Arrays.sort(cs); // 排序保证一组字母异位词同一 key
        ht.computeIfAbsent(new String(cs), k -> new ArrayList<>()).add(str);
    }
    return new ArrayList(ht.values());
}
```

## LC128.最长连续序列

[128. 最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)

#### 题目描述

给定一个为排序的整数数组 nums，找出数字连续的最长序列的长度。

示例：输入：nums = [100, 4, 200, 1, 3, 2]；输出：4，最长连续数字序列为 [1, 2, 3, 4]

#### 解题思路

理解题意很关键，我们要求最长数字连续序列的长度，首先得理解什么叫数字连续？即 n,n+1,n+2,n+3... 这样的序列。那么我们要求这样一个序列，我们可以直接对原数组 nums 由小到大排序，判断求每个数开头的连续序列的最大长度，在这个过程中更新最大长度。

```java
public int longestConsecutive(int[] nums) {
	int n = nums.length;
    int longestStep = 1;
    Arrays.sort(nums);
    int step = 1;
    for (int i = 0; i < n - 1; i++) {
        if (nums[i + 1] == nums[i]) {
            step += 1;
        } else if (nums[i + 1] == nums[i]) {
            //... no operate
        } else {
            longestStep = Math.max(longestStep, step);
            step = 1;
        }
    }
    return logestStep;
}
```

但是我们知道排序的时间复杂度为 nlogn，那么我们能否借助哈希表进一步提升效率呢？可以，但是在哈希表的处理过程中，我们需要避免重复查找，即如果 num - 1 已经在哈希表存在，我们就不判断 num 开始的连续序列。

```java
public int longestConsecutive(int[] nums) {
	int n = nums.length;
    int longestStep = 1;
    Set<Integer> ht = new HashSet<>();
    for (int num : nums) ht.add(num);
    
    for (int num : nums) {
        if (ht.contains(num - 1)) continue;
        int curNum = num;
        int step = 1;
        while (ht.contains(curNum + 1)) {
            curNum++；
            step++;
        }
        longestStep = Math.max(longestStep, step);
    }
    return logestStep;
}
```



## LC290.单词规律

[290. 单词规律](https://leetcode.cn/problems/word-pattern/)

根据题目的意思，就是针对 pattern 里的每一个字母，唯一对应 s 中的一个单词，所以理所当然用 hashmap 来实现，写出了以下代码：

```java
public boolean wordPattern(String pattern, String s) {
    String[] ss = s.split(" ");
    int n = pattern.length();
    if (n != ss.length) return false;
    Map<Character, String> map = new HashMap<>();
    for (int i = 0; i < n; i++) {
        char c = pattern.charAt(i);
        if (!map.containsKey(c)) map.put(c, ss[i]);
        else {
            if (map.get(c) != ss[i]) {
                return false;
            }
        }
    }
    return true;
}
```

发现 `pattern = "abba"; s = "dog cat cat dog"` 的测试用例得不到正确答案，懵了。Why?Why?Why?

**java 中字符串的比较必须用 equals 方法**

```java
public boolean wordPattern(String pattern, String s) {
    String[] ss = s.split(" ");
    int n = pattern.length();
    if (n != ss.length) return false;
    Map<Character, String> map = new HashMap<>();
    for (int i = 0; i < n; i++) {
        char c = pattern.charAt(i);
        if (!map.containsKey(c)) map.put(c, ss[i]);
        else {
            if (!map.get(c).equals(ss[i])) {
                return false;
            }
        }
    }
    return true;
}
```

结果发现又错了.....是必须一一对应，必须互相一一对应，以上的解法针对以下用例：就不合适了

```txt
pattern = "abba"
s = "dog dog dog dog"
```

正确解法：

```java
public boolean wordPattern(String pattern, String s) {
    String[] ss = s.split(" ");
    int n = pattern.length();
    if (n != ss.length) return false;
    Map<Character, String> mapp = new HashMap<>();
    Map<String, Character> maps = new HashMap<>();
    for (int i = 0; i < n; i++) {
        char c = pattern.charAt(i);
        String str = ss[i];
        if (!mapp.containsKey(c)) mapp.put(c, str);
        else {
            if (!mapp.get(c).equals(str)) return false;
        }
        if (!maps.containsKey(str)) maps.put(str, c);
        else {
            if (!maps.get(str).equals(c)) return false;
        }

    }
    return true;
}
```

## LC219.存在重复元素II

[219. 存在重复元素 II](https://leetcode.cn/problems/contains-duplicate-ii/)

要判断数组 nums 中的两个相同元素，然后存在下标 i 和 j 的距离不超过 k 就返回 true，不存在就返回 false。

我们对数组的遍历都是从 0 到 n-1 或者 从 n-1 到 0。需要在处理当前元素的时候能知道已遍历元素里是否出现过相同元素，有相同则判断距离，而且我们需要处理完所有元素，所以判断距离后，我们需要更新重复元素为距离之后最近的下标。

由此思想，我们需要记录元素和下标的对应关系，并且随着索引增大，对应重复的记录元素的下标也应该一直以最新的为主。

```java
public boolean containsNearbyDuplicate(int[] nums, int k) {
    int n = nums.length;
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < n; i++) {
        if (map.containsKey(nums[i])) {
            int j = map.get(nums[i]);
            if (i - j <= k) return true;
        }
        map.put(nums[i], i);
    }
    return false;
}
```

