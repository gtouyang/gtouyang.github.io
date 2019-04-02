---
title: Leetcode-longest-consecutive-sequence
date: 2019-03-30 15:13:01
categories: 算法
tags:
- Leetcode
- Java
- Array
- Retrieve
---
题目描述

Given an unsorted array of integers, find the length of the longest consecutive elements sequence.

For example,
Given[100, 4, 200, 1, 3, 2],
The longest consecutive elements sequence is[1, 2, 3, 4]. Return its length:4.

Your algorithm should run in O(n) complexity.
<!--more-->
---

这一题我们可以用一个hash集合记录我们有多少数没查，然后我们遍历数组种的数，如果数在集合中，说明它还没被查，然后检查有没有连续的数，有则移出集合，然后继续找。这里为什么要移出集合呢，我们举个例子

现在有集合[100, 4, 200, 1, 3, 2]，我们按100，4，200，1，3，2找。

我们移出100，得[4, 200, 1, 3, 2]，然后找101和99，没找到。

然后我们移出4，得[200, 1, 3, 2]，然后找5和3，找到3。移出3，得[200, 1, 2]，找2，移出2，[200, 1]， 找1，移出1，得[200]，找0，没找到。

然后当我们移出200，得[]，找201和199，没有

然后我们找1，发现1不在集合中，说明1找过了，因为与1连续的1234和与4连续的1234重复了，我们不用再找1连续的数字

同理3和2也不找，结束，最长是4

这里我们用hash集合的原因是hash集合是计算hash值来找位置的，我们判断某个数在不在集合中的复杂度是O(1)，因为我们不用遍历整个集合。所以最后的复杂度能保持再O(n)。



```java
import java.util.*;

public class Solution {
    public int longestConsecutive(int[] num) {
        if(num == null || num.length == 0){
            return 0;
        }

        HashSet<Integer> s = new HashSet<Integer>();

        for(int n : num){
            s.add(n);
        }

        int res = 0;

        for(int n : num){
            if(s.remove(n)){
                int pre = n - 1;
                int next = n + 1;
                while(s.remove(pre)){
                    pre --;
                }
                while(s.remove(next)){
                    next ++;
                }
                res = Math.max(res, next-pre-1);
            }
        }

        return res;
    }
}
```
