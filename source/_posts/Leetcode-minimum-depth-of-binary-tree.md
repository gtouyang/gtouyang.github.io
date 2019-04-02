---
title: minimum-depth-of-binary-tree
date: 2019-03-29 21:21:16
categories: 算法
tags:
- Leetcode
- Java
- Tree
---


题目描述

Given a binary tree, find its minimum depth.The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

<!--more-->
---

这一题比较简单,通过递归遍历左右子树，然后比较左右子树的深度，返回浅的子树的深度。


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
    public int run(TreeNode root) {
        if(root == null){
            return 0;
        }

        //如果两边都不为null
        else if(root.left != null && root.right != null){
            int minLeft = run(root.left);
            int minRight = run(root.right);

            //判断哪一边更浅
            if(minLeft<minRight){
                return minLeft+1;
            }else{
                return minRight+1;
            }

        //如果只是左不为null
        }else if(root.left!=null){
            return run(root.left)+1;

        //如果只是右不为null或左右都是null
        }else{
            return run(root.right)+1;
        }
    }
}
```
