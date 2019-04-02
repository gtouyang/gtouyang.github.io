---
title: single-number
date: 2019-03-30 12:32:24
categories: 算法
tags:
- Leetcode
- Java
---
题目描述

Given an array of integers, every element appears twice except for one. Find that single one.

Note: 
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?
<!--more-->
---
这一题可以用异或来实现，当一个数a被另一个数b(b可以等于a)异或两次时，a不变。例如 `0^b^a^c^c^b = a`

```java
public class Solution {
    public int singleNumber(int[] A) {
        int res = 0;
        for(int a:A){
            res ^= a;
        }
        return res;
    }
}
```

