---
title: evaluate-reverse-polish-notation
date: 2019-03-29 21:50:20
categories: 算法
tags:
- Leetcode
- Java
---

题目描述

Evaluate the value of an arithmetic expression in Reverse Polish Notation.

Valid operators are+,-,*,/. Each operand may be an integer or another expression.

Some examples:

	  ["2", "1", "+", "3", "*"] -> ((2 + 1) * 3) -> 9
	  ["4", "13", "5", "/", "+"] -> (4 + (13 / 5)) -> 6

<!--more-->
---

这一题是考波兰数，我们可以通过栈来解决，将数字放入栈中，读到运算符就从栈中拿两个数出来运算，然后将运算结果放进栈中，到执行结束后栈中剩下的数就是结果。

```java
import java.util.*;

public class Solution {
    public int evalRPN(String[] tokens) {
        Stack<Integer> stack = new Stack<Integer>();
        int len = tokens.length;
        for(int i = 0; i < len; i++){
            if(tokens[i].equals("+")){
                int t1 = stack.pop();
                int t2 = stack.pop();
                stack.push(t2+t1);
            }else if(tokens[i].equals("-")){
                int t1 = stack.pop();
                int t2 = stack.pop();
                stack.push(t2-t1);
            }else if(tokens[i].equals("*")){
                int t1 = stack.pop();
                int t2 = stack.pop();
                stack.push(t2*t1);
            }else if(tokens[i].equals("/")){
                int t1 = stack.pop();
                int t2 = stack.pop();
                stack.push(t2/t1);
            }else{
                stack.push(Integer.parseInt(tokens[i]));
            }
        }
        return stack.pop();
    }
}
```
