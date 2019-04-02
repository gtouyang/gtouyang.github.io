---
title: Leetcode-word-break-ii
date: 2019-03-30 00:09:24
categories: 算法
tags:
- Leetcode
- Java
- Dynamic Programming
---
题目描述

Given a string s and a dictionary of words dict, add spaces in s to construct a sentence where each word is a valid dictionary word.

Return all such possible sentences.

For example, given
s ="catsanddog",
dict =["cat", "cats", "and", "sand", "dog"].

A solution is["cats and dog", "cat sand dog"].
<!--more-->
---

这一题我们不只是要判断能不能分词，而是要把单词分出来，而且是所有的分词方案。我们可以用一个Hashmap记录哪些字符串可以分，能分成什么样的形式，这样当我们遇到相同的字符串时，就可以查询调用。

第一种时一个字母一个字母的移动，找到一个单词后，递归调用，获取这个单词后面的字符串分词的全部方案，然后将这个单词添加到方案中，这里要注意顺序，要放在字符串前面。然乎我们还要继续移动字母，看有没有另一个单词(字符串中包括了上一个单词)。例如我们找到了单词ban，我们收集完ban的方案后还得去找是不是banana，如果是，再收集banana的方案。

第二种方法是从后往前找，可读性不如第一种，找到单词后发生递归调用，然后也是找剩下的字符串。

```java
import java.util.*;

public class Solution {
    public ArrayList<String> wordBreak(String s, Set<String> dict) {
        //下面的map用于记录已经被拆分过的字符串，提高效率
        Map<String, ArrayList<String>> memo = new HashMap<String, ArrayList<String>>();
        return wordBreakHelper(s, dict, memo);
    }

    public ArrayList<String> wordBreakHelper(String s,
                                            Set<String> dict,
                                            Map<String, ArrayList<String>> memo){

        //如果表中有以及计算过的字符串，那么从表中返回结果
        if (memo.containsKey(s)){
            return memo.get(s);
        }

        ArrayList<String> results = new ArrayList<String>();

        //如果字符串为空，那么返回一个空的数组
        if (s.length() == 0){
            return results;
        }

        //如果字符串属于dict中的元素，将该字符串放入数组中并返回
        if (dict.contains(s)){
            results.add(s);
        }
        for (int len = 1; len < s.length(); len++){
            String word = s.substring(0, len);

            //除非word属于dict，否则直接进入下一循环
            if (!dict.contains(word)){
                continue;
            }

            //将剩下的字符串递归调用
            String suffix = s.substring(len);

            //收集递归的结果
            ArrayList<String> segmentations = wordBreakHelper(suffix, dict, memo);

            //将递归的结果分别与word合并
            for (String segmentation : segmentations){
                results.add(word + " " + segmentation);
            }
        }
        //将此次运行的结果记录在map中
        memo.put(s, results);

        return results;
    }
}

//第二种方法
public class Solution{
    public ArrayList<String> list = new ArrayList<String>();
    public ArrayList<String> wordBreak(String s, Set<String> dict) {
        dfs(s, s.length(), "", dict);
        return list;
    }

    private void dfs(String s, int index, String str, Set<String> dict){
        
        //如果被调用时index已不大于零，则说明已找完所有单词，若找到则str长度大于零
        if (index <= 0 && str.length()>0){
            list.add(str.substring(0, str.length()-1));
        }

        //从后往前数
        for (int i = index; i >= 0; i--){

            //如果找到正确的单词，则发送递归调用
            if(dict.contains(s.substring(i, index))){

                //将新找到的单词和之前找到的合并
                dfs(s, i, s.substring(i, index) + " " + str, dict);
            }
        }
    }
}
```

