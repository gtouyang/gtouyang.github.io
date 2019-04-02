---
title: Leetcode-palindrome-partitioning
date: 2019-03-30 14:09:24
categories: 算法
tags:
- Leetcode
- Java
- String
---
Given a string s, partition s such that every substring of the partition is a palindrome.

Return all possible palindrome partitioning of s.

For example, given s ="aab",
Return

	  [
	    ["aa","b"],
	    ["a","a","b"]
	  ]
<!--more-->
---
这一题让我们从字符串中分出回文，并分出所有的分法，和之前的分词类似，不过将字符串判断是不是单词(在单词表中)变成判断是不是回文。我们可以先找出所有回文，用一个二维布尔数组记住结果isPalindrome[i][j]表示从字符串i位到j位是不是回文，然后就和分词一样了。一个字符一个字符的试，试到时回文，就递归调用剩下的字符串。下面两种方法原理上是相似的。一个是等递归返回后放入结果再将结果返回。另一个是有一个全局变量res，找到回文后将回文放入递归调用的参数list中，被调用的方法发现没有剩余的字符串时将接受的list放入list中。一个是等下级返回结果自己再合并，另一个是合并上级传入的结果后往下级传递新的结果。

```java
import java.util.*;

public class Solution {
    private HashMap<String, ArrayList<String>> map;

    private boolean[][] isPalindrome;

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

    private ArrayList<ArrayList<String>> partitionHelper(String s, int start, int end) {

        ArrayList<ArrayList<String>> res = new ArrayList<ArrayList<String>>();;

        if(start == end){
            ArrayList<String> t = new ArrayList<String>();
            t.add(s.substring(start, end+1));
            res.add(t);
            return res;
        }

        if(start > end){
            ArrayList<String> t = new ArrayList<String>();
            res.add(t);
            return res;
        }

        for (int i = end; i >= start; i--){
            if(isPalindrome[start][i]){
                ArrayList<ArrayList<String>> partitions = partitionHelper(s, i+1, end);
                for (ArrayList<String> a : partitions){
                    a.add(0, s.substring(start, i+1));
                    res.add(a);
                }
                
            }
        }
        return res;
    }
	public ArrayList<ArrayList<String>> partition(String s) {
        map = new HashMap<String, ArrayList<String>>();

        isPalindrome = getIsPalindrome(s);

        ArrayList<ArrayList<String>> res = partitionHelper(s, 0, s.length()-1);

        return res;
    }
}

//回溯法
public class Solution {
    public ArrayList<ArrayList<String>> partition(String s) {
        ArrayList<ArrayList<String>> res = new ArrayList<ArrayList<String>>();
        if(s == null || s.length() == 0){
            return res;
        }

        solve(s, 0, new ArrayList<String>(), res);
        return res;
    }

    private void solve(String s, int index, ArrayList<String> preList, ArrayList<ArrayList<String>> res) {
        if (index == s.length()){
            //如果已经分完了，就将这次分的结果放入res里
            res.add(new ArrayList<String>(preList));
            return;
        }

        //新建一个list包含preList
        ArrayList<String> list = new ArrayList<String>(preList);
        for (int i = index+1; i <= s.length(); i++){
            if (isPalindrome(s.substring(index, i))){
                list.add(s.substring(index, i));
                //如果是回文，就加入到list中，剩下的递归
                solve(s, i, list, res);
                //添加完在res后清除list
                list.remove(list.size()-1);
            }
        }
    }

    private boolean isPalindrome(String s){
        if(s == null){
            return false;
        }

        int l = 0, r = s.length() - 1;
        while (l < r) {
            //从两边逐渐往中间走，只要有一个不对就不是回文
            if (s.charAt(l++) != s.charAt(r--)){
                return false;
            }
        }
        return true;
    }
}
```
