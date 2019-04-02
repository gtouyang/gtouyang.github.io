---
title: Leetcode-word-break
date: 2019-03-30 00:03:43
categories: 算法
tags:
- Leetcode
- Java
- Dynamic Programming
---
题目描述

Given a string s and a dictionary of words dict, determine if s can be segmented into a space-separated sequence of one or more dictionary words.

For example, given
s ="leetcode",
dict =["leet", "code"].

Return true because"leetcode"can be segmented as"leet code".
<!--more-->
---

这一题只要判断能不能分词，我们可以用一个boolean数组，记录第几位之前能被分词，那么我们在找到一个新单词的时候，我们可以判断这个单词之前的内容能不能被分，如果也能，那就说明到新单词为止的字符串都能分词。下面我们逐个字母的移动，找j到i的字符串是不是单词，如果是，且i之前可分，那么j之前也可以分。

```java
import java.util.*;
public class Solution {
    public boolean wordBreak(String s, Set<String> dict) {
        int len = s.length();

        //记录字符串中的前n位能否被分词
        boolean[] arrays = new boolean[len + 1];
        arrays[0] = true;
        for (int i = 1; i <= len; i++){
            for (int j = 0; j < i; j++){

                //如果j之前的字符串能分词且j到i的字符组成新的单词，那么记录i之前的字符串能分词
                if(arrays[j] && dict.contains(s.substring(j,i))){
                    arrays[i] = true;
                    break;
                }
            }
        }
        return arrays[len];
    }
}
```
