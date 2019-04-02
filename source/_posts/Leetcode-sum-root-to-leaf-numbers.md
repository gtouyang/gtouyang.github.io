---
title: Leetcode-sum-root-to-leaf-numbers
date: 2019-03-30 15:03:18
categories: 算法
tags:
- Leetcode
- Java
- Tree
---
题目描述

Given a binary tree containing digits from0-9only, each root-to-leaf path could represent a number.

An example is the root-to-leaf path1->2->3which represents the number123.

Find the total sum of all root-to-leaf numbers.

For example,

	    1
	   / \
	  2   3

The root-to-leaf path1->2represents the number12.
The root-to-leaf path1->3represents the number13.

Return the sum = 12 + 13 =25.
<!--more-->
---

如上面的例子所示，当我们加root时，我通过递归调用将自己的val传给左右孩子，然后让左右孩子返回自己的值，例如我将1传给2，2将接受的参数×10加上自己的val，因为自己没有孩子，所以自己的值就是12，然后返回，同理接收到右孩子的返回后，1得到12和13，将12+13返回就是结果。

若树是

	    1
	   / \
	  2   3
	 / \
	4   5

则1将自己传给2和3，2将12传给4和5，4和5分别返回124和125，然后2返回249，3返回13，最后1返回262。

```java
/**
 * Definition for binary tree
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
public class Solution {
    public int sumNumbers(TreeNode root) {
        int sum = 0;
        if(root == null){
            return 0;
        }
        return sumNumbersHelper(root, sum);
        
    }
    
    private int sumNumbersHelper(TreeNode root, int sum){
        sum = sum * 10 + root.val;

        if(root.left == null && root.right == null){
            return sum;
        }

        int left_v = 0;
        int right_v = 0;
        if(root.left != null){
            left_v = sumNumbersHelper(root.left, sum);
        }
        if(root.right != null){
            right_v = sumNumbersHelper(root.right, sum);
        }
        return left_v + right_v;
    }
}
```
