---
title: Leetcode-single-number-ii
date: 2019-03-30 12:36:07
categories: 算法
tags:
- Leetcode
- Java
---
题目描述

Given an array of integers, every element appears three times except for one. Find that single one.

Note: 
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?
<!--more-->
---

这一题难点在于重复的数会出现3次而不是2次、4次之类的偶数，不能直接通过异或简单处理，需要做一些调整。

假设其中一个a为0010
那么：

1.

    one = (0000 ^ 0010) & ~0000
        = 1101 & 1111
        = 1101
    
    two = (0000 ^ 0010) & ~1101
        = 1101 & 0010
        = 0000

2.

    one = (1101 ^ 0010) & ~0000
        = 0000 & 1111
        = 0000
    
    two = (0000 ^ 0010) & ~0000
        = 1101 & 1111
        = 1101

3.

    one = (0000 ^ 0010) & ~ 1101
        = 1101 & 0010
        = 0000
    
    two = (1101 ^ 0010) & ~0000
        = 0000 & 1111
        = 0000

显然当与只出现一次的数运算时，one会变成那个数，而如果那个数出现三次或两次，那么就会变回0


```java
public class Solution {
    public int singleNumber(int[] A) {
        int one = 0;
        int two = 0;
        for(int a:A){
            one = (one ^ a) & ~two;
            two = (two ^ a) & ~one;
        }
        return one;
    }
}
```
