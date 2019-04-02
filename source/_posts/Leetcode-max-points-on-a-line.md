---
title: max-points-on-a-line
date: 2019-03-29 22:11:23
categories: 算法
tags:
- Leetcode
- Java
---
题目描述

Given n points on a 2D plane, find the maximum number of points that lie on the same straight line.
<!--more-->
---

这一题可以先选择一个点P，然后遍历这个点P以外的点和P的斜率，斜率相同则在同一条直线上。在计算斜率时，可以不考虑P之前的已经被算过的点，因为这些点和P所在的直线在就算过，不比重复计算。

```java
/**
 * Definition for a point.
 * class Point {
 *     int x;
 *     int y;
 *     Point() { x = 0; y = 0; }
 *     Point(int a, int b) { x = a; y = b; }
 * }
 */
public class Solution {
    public int maxPoints(Point[] points) {
        if(points == null){
            return 0;
        }
        
        int len = points.length;
        
        if(len <= 2){
            return len;
        }
        
        int res = 0;

        //遍历每一个点
        for (int i = 0; i < len; i++){
            HashMap<String, Integer> map = new HashMap<>();
            int duplicate = 0; int cnt = 0;

            //遍历i点以外的点，计算i点和j点的斜率
            for (int j = i+1; j < len; j++){
                int diffx = points[i].x - points[j].x;
                int diffy = points[i].y - points[j].y;
                if(diffx == 0 && diffy == 0){
					//若2个点重合，则它可以在这个点的任意直线上
                    duplicate ++;
                    continue;
                }

                //计算最大公因子
                int gcd = getGCD(diffx, diffy);
                
                //化成最简分数
                if (gcd != 0){
                    diffx /= gcd;
                    diffy /= gcd;
                }

                //将斜率以分子和分母的形式存在哈希表中
                //不采取浮点数的原因是防止浮点数丢失精度
                String key = new String(diffx) + "_" + new String(diffy);
                map.put(key, map.getOrDefault(key, 0) + 1);
                
                //如果在当前直线上的点的数目高于cnt，则更新cnt
                cnt = Math.max(cnt, map.get(key));
            }
            //如果在与当前点在一条直线上的点的数目高于res，则更新res
            res = Math.max(res, duplicate + cnt + 1);
        }
    }
    private int getGCD(int diffx, int diffy) {
        //TODO 计算 diffx和diffy的最大公因数
        if(diffy == 0){
            return diffx;
        }
        return getGCD(diffy, diffx % diffy);
    }
}
```

