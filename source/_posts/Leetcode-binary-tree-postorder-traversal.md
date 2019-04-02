---
title: binary-tree-postorder-traversal
date: 2019-03-29 22:43:59
categories: 算法
tags:
- Leetcode
- Java
- Tree
---
题目描述

Given a binary tree, return the postorder traversal of its nodes' values.

For example:
Given binary tree{1,#,2,3},

```
1
  \
   2
  /
3
```

return[3,2,1].

Note: Recursive solution is trivial, could you do it iteratively?
<!--more-->
---

这题考的是后序遍历树的结点，而且要求使用迭代的方法(因为递归法解这一题过于简单？)。记录上一个根时什么，在栈中看当前根，如果上一个根是当前根的孩子，这说明这个根的孩子遍历完了，可以把这个根放入result中，若没有，将左右孩子放入栈中，下个循环后序遍历左右孩子。


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
    public ArrayList<Integer> postorderTraversal(TreeNode root) {
        //TODO 用迭代法后序遍历
        ArrayList<Integer> result = new ArrayList<Integer>();

        if (root == null){
            return result;
        }

        Stack<TreeNode> stack = new Stack<TreeNode>();

        TreeNode pre = null;
        stack.push(root);

        while(!stack.isEmpty()){
            TreeNode cul = stack.peek();

            //当前结点左右孩子为空或左右孩子已经处理完了就处理该结点
            if ((cur.left == null && cur.right == null) || (pre != null && (pre == cur.left || pre == cur.right))){
                result.add(cur.val);
                stack.pop();
                pre = cur;
            }else{
                //右孩子先进栈，使左孩子在栈顶，因为要先遍历左孩子
                if(cur.right != null){
                    stack.push(cur.right);
                }
                if(cur.left != null){
                    stack.push(cur.left);
                }
            }
        }
        return result;

    }
}

public class Solution {
    public ArrayList<Integer> postorderTraversal(TreeNode root) {
        //TODO 用递归法后序遍历
        ArrayList<Integer> result = new ArrayList<Integer>();

        if (root == null){
            return result;
        }

        result.addAll(postorderTraversal(root.left));
        result.addAll(postorderTraversal(root.right));
        result.add(root.val);

        return result;

    }
}
```