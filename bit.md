# bit

[TOC]

reference link:

- https://leetcode.cn/problems/power-of-two/solutions/796201/2de-mi-by-leetcode-solution-rny3/?envType=study-plan-v2&envId=primers-list

第一个技巧：n & (n - 1) 可以将 n 二进制表示的最低位的 1 移除。所以如果 n 是正整数且 `n & (n -1) = 0​`，那么 n 就是 2 的幂。

第二个技巧：n & (-n) 可以直接获取 n 二进制表示的最低位的 1，因为负数在计算机中按照补码规则存储，-n 的二进制表示 n 的二进制表示的每一位取反再加上 1。如果 n 是 2 的幂，则 n 的二进制表示中只有一个 1，所以 n & (-n) = n。

## LC231.2的幂

常规的递归写法：

```java
public boolean isPowerOfTwo(int n) {
    if (n == 0) return false;
    if (n == 1) return true;
    if (n % 2 == 0) return isPowerOfTwo(n / 2);
    else return false;
}
```

二进制思路：

```java
public boolean isPowerOfTwo(int n) {
    return n > 0 && (n & (n - 1)) == 0;
}
```

## LC326.3的幂

[326. 3 的幂](https://leetcode.cn/problems/power-of-three/)

```java
public boolean isPowerOfThree(int n) {
	if (n == 0) return false;
    if (n == 1) return true;
    if (n % 3 == 0) return isPowerOfThree(n / 3);
    else return false;
}
```

或者迭代：

```java
public boolean isPowerOfThree(int n) {
    while (n != 0 && n % 3 == 0) {
        n /= 3;
    }
    return n == 1;
}
```

## LC67.二进制求和

[67. 二进制求和](https://leetcode.cn/problems/add-binary/)

加法求和的解题思路都是一致的，都是从个位开始两两相加，然后根据进位依次推到更高位，所以唯一需要注意的就是进位的处理和数组或者字符串的反转。

> java 中没有提供数组反转的内置函数，需要自己实现，针对集合倒是有 Collections.reverse(collction)；
> 针对字符串需要转为 StringBuilder 然后调用 reverse 反转。或者就从后往前遍历。

```java
public String addBinary(String a, String b) {
    StringBuilder ans = new StringBuilder();
    int carry = 0;
    for(int i = a.length() - 1, j = b.length() - 1;i >= 0 || j >= 0; i--, j--) {
        int sum = carry;
        sum += i >= 0 ? a.charAt(i) - '0' : 0;
        sum += j >= 0 ? b.charAt(j) - '0' : 0;
        ans.append(sum % 2);
        carry = sum / 2;
    }
    ans.append(carry == 1 ? carry : "");
    return ans.reverse().toString();
}
```
