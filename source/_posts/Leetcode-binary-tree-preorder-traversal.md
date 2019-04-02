---
title: binary-tree-preorder-traversal
date: 2019-03-29 23:17:03
categories: 算法
tags:
- Leetcode
- Java
- Tree
---
题目描述

Given a binary tree, return the preorder traversal of its nodes' values.

For example:
Given binary tree{1,#,2,3},

```java
   1
    \
     2
    /
   3
```

return[1,2,3].

Note: Recursive solution is trivial, could you do it iteratively?


<!--more-->
---

先序遍历跟后序遍历类似，只是顺序不同，这里也是要求用的迭代法。可以通过栈来实现，每次从栈中拿出一个根放入result中，并将左右孩子进栈，这样在下次循环中便会遍历左右孩子。

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

import java.util.*;

public class Solution {
    public ArrayList<Integer> preorderTraversal(TreeNode root) {
        //TODO 用迭代法前序遍历
        ArrayList<Integer> result = new ArrayList<Integer>();

        if (root == null){
            return result;
        }
        
        Stack<TreeNode> stack = new Stack<TreeNode>();
        TreeNode pre = null;
        stack.push(root);

        while(!stack.isEmpty()){
            //将当前结点(根)放入result中，并将左右孩子放入栈中，右孩子先进，让左孩子在栈顶
            TreeNode cur = stack.pop();
            result.add(cur.val);
            if(cur.right != null){
                stack.push(cur.right);
            }
            if(cur.left != null){
                stack.push(cur.left);
            }
        }

        return result;
    }
}

public class Solution {
    public ArrayList<Integer> preorderTraversal(TreeNode root) {
        //TODO 用递归法前序遍历
        ArrayList<Integer> result = new ArrayList<Integer>();

        if (root == null){
            return result;
        }

        result.add(root.val);
        result.addAll(preorderTraversal(root.left));
        result.addAll(preorderTraversal(root.right));
        return result;
    }
}
```
