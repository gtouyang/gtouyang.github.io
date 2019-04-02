---
title: Leetcode-gas-station
date: 2019-03-30 12:47:56
categories: 算法
tags:
- Leetcode
- Java
- Greed
---
题目描述

There are N gas stations along a circular route, where the amount of gas at station i isgas[i].

You have a car with an unlimited gas tank and it costscost[i]of gas to travel from station i to its next station (i+1). You begin the journey with an empty tank at one of the gas stations.

Return the starting gas station's index if you can travel around the circuit once, otherwise return -1.

Note: 
The solution is guaranteed to be unique.
<!--more-->
---
我们先记住每个站油的变化，就是将这一战获得的油减去去下一站的耗油，就是净油量变化，我们先从第一个站开始，假设我们走到第i个站发现油不够了，说明以第一个站为起点行不通，那么我们是不是要从第二个站再算一次呢？其实是不用的，假设我们从第n(0<n<i)站开始，我们已知从第一个站出发到底第n个站是可行的，即tank[n]>0的，我们如果从n出发，那么到底i时剩油量就是tank[i]-tank[n]，也就是我们的油更少了。即我们发现到i站不行后，可以以i+1站为起点再尝试，i之前的无需再试。

total记录这个旅程的净油量变化，期间可以为负，当走完整个旅程后total是负的，就说明所有的油加起来走不完所有的路，即不可能走完。那么如果total是正的，则一定存在正确的方案。

假定程序之前有几次尝试没有成功，将路分成`[a][b][c][d]`,因为之前都不成功，那么`a<0`, `b<0`, `c<0`，最后走完`total>0`, 即`a+b+c+d>0`, 那么容易得到`d>0`。路是环形，我们再从起点出发，此时油量不为0，而是d,显然到a段终点前肯定能走(之前油为0出发都能走，现在油为d肯定可以)到达a段终点时，虽然`a<0`，但是`a+d>0`(轻松得出)，同理`a+b+d>0`, 再加上`a+b+c+d>0`，那么从d段起点出发肯定能走完。

综上所述，只要total>0，那么肯定能走完，起点为index(上一个方案走不通的站的下一站)

```java
public class Solution {
    public int canCompleteCircuit(int[] gas, int[] cost) {
        int len = gas.length;

        //计算经过每个站点油的变化
        for (int i = 0; i < len; i++){
            gas[i] -= cost[i];
        }
        
        int index = 0;
        int tank = 0;
        int total = 0;

        for (int j = 0; j < len; j++){
            tank += gas[j];
            total += gas[j];

            //若油箱小于0，则说明油不够，以当前站点的下一点为起点再试
            if(tank < 0){
                tank = 0;
                index = j+1;
            }
        }
        
        //若全部走完油是负的，那怎么走都不可能走完
        if(total < 0){
            index = -1;
        }
        
        return index;
    }
}
```
