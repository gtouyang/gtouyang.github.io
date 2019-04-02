---
title: Leetcode-candy
date: 2019-03-30 12:40:11
categories: 算法
tags:
- Leetcode
- Java
- Dynamic Programming
---
题目描述

There are N children standing in a line. Each child is assigned a rating value.

You are giving candies to these children subjected to the following requirements:

Each child must have at least one candy.
Children with a higher rating get more candies than their neighbors.
What is the minimum candies you must give?
<!--more-->
---

我们可以先逐个给糖，如果后面的孩子比前面的孩子高分，则要多给一颗，如果一样分，那么先只给一颗。如果后面的孩子比前面的孩子地方，那么要检查一下前面的孩子的糖是否比后面的多，如果不是，补给前面的孩子一颗糖，一直补到递增结束，即发糖符合规则。

```java
public class Solution {

    public int eachCandy[];

    public int candy(int[] ratings) {

        if(ratings == null || ratings.length == 0){
            return 0;
        }

        int sum = 0;
        eachCandy = new int[ratings.length];
        eachCandy[0] = 1;

        for (int i = 1; i < ratings.length; i++){

            //如果后面的孩子比前面的孩子高分，则后面的孩子比前面的孩子多一颗糖
            if (ratings[i] > ratings[i-1]){
                eachCandy[i] = eachCandy[i-1]+1;

            //如果后面的孩子比前面的孩子低分且前面的孩子只有一颗糖，则只给后面的孩子一颗糖，并启动补偿策略
            }else if (ratings[i] < ratings[i-1] && eachCandy[i-1] == 1){
                eachCandy[i] = 1;
                compensate(ratings, i);

            //其他情况只给一颗糖，即：1.后面的孩子比前面的孩子低分但前面的孩子有两颗糖以上  2.后面的孩子与前面的孩子同分
            }else{
                eachCandy[i] = 1;
            }
        }

        //统计给了几颗糖
        for (int c:eachCandy){
            sum += c;
        }
        return sum;
    }

    //补偿策略
    //如果前面的孩子比后面的孩子高分而糖果不比他多，则补偿他一颗糖，直到递增结束(即前面的孩子比后面的孩子高分而糖果不比他多这一条件不被满足)
    public void compensate(int[] ratings, int index){
        for(int i = index-1; i >= 0; i--){
            if(ratings[i] > ratings[i+1] && eachCandy[i] <= eachCandy[i+1]){
                eachCandy[i]++;
            }else{
                break;
            }
        }
    }
}
```


除此之外还有一种方法，先正向遍历，如果后面的孩子比前面的孩子高分，加一颗糖，同分或低分只给一颗，遍历完成后再按此规则反向遍历检查一遍，补偿点糖，使得正反遍历都满足规则即可。

