# hash table

[toc]

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

