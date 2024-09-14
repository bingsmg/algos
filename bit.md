# bit

[TOC]

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
