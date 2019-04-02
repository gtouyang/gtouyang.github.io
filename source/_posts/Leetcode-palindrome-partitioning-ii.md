---
title: Leetcode-palindrome-partitioning-ii
date: 2019-03-30 14:36:09
categories: 算法
tags:
- Leetcode
- Java
- Dynamic Programming
---
题目描述

Given a string s, partition s such that every substring of the partition is a palindrome.

Return the minimum cuts needed for a palindrome partitioning of s.

For example, given s ="aab",
Return1since the palindrome partitioning["aa","b"]could be produced using 1 cut.
<!--more-->
---
这一题我们虽然也可以把所有方案分出来(像上一个那样)，但是这一未免有些过于复杂，我们可以换一种思路。现有一个字符串abaded，我们可以判断a要切几刀，a是回文，我们记下前一个字符串可以切0刀。然后我们判断ab，同理记下前2个字符窜可以分，切1刀。然后判断aba，这时我们发现，有2种方案，分别是"a","b","a"和"aba"。那么我们要比较在切1刀的基础上再切1刀(切2刀)少还是切0刀少，显然，我们记下前3个字符最少切法是切0刀。依此类推，我们每次发现第j刀i的字符串是回文，然后前i个字符串的最少切法就是前j个字符串的最少切法上加一刀。

```java
import java.util.*;

public class Solution {

    //判断start到end处是不是回文
    private boolean isPalindrome(String s, int start, int end) {
        for(int i = start, j = end; i < j; i++, j--){
            if(s.charAt(i) != s.charAt(j)){
                return false;
            }
        }
        return true;
    }

    //获取一个字符串中哪里有回文
    private boolean[][] getIsPalindrome(String s) {
        int len_s = s.length();

        //如果isPalindrome[i][j] = true，就说明s的i到j处是回文
        boolean[][] isPalindrome = new boolean[len_s][len_s];

        //所有单字母都是回文
        for (int i = 0; i < len_s; i++){
            isPalindrome[i][i] = true;
        }

        //如果两个相同的字母相邻，这两个字母组成的字符串是回文
        for (int i = 0; i < len_s -1; i++){
            isPalindrome[i][i+1] = (s.charAt(i) == s.charAt(i+1));
        }

        //如果一个字符串头尾相同，且去头去尾后是回文，那么它也是回文
        for (int length = 2; length < len_s; length++){
            for (int start = 0; start+length < len_s; start++){
                isPalindrome[start][start+length] = (isPalindrome[start+1][start+length-1] && s.charAt(start) == s.charAt(start+length));
            }
        }

        return isPalindrome;
    }

    public int minCut(String s) {
        if(s == null || s.length() == 0){
            return 0;
        }

        boolean[][] isPalindrome = getIsPalindrome(s);

        int len_s = s.length();

        //f[i]记录前i个字母的最少分割次数
        int[] f = new int[len_s + 1];
        for (int i = 0; i <= len_s; i++){
            f[i] = i - 1;
        }

        for (int i = 1; i <= len_s; i++){
            for (int j = 0; j < i; j++){
                if (isPalindrome[j][i-1]){

                    //如果j到i-1是回文，那么如果前j个字母的最少分割次数+1少于当前前i个字母最少分割次数，则更新
                    f[i] = Math.min(f[i], f[j]+1);
                }
            }
        }

        return f[len_s];
    }
}
```
