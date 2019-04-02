---
title: linked-list-cycle
date: 2019-03-29 23:39:48
categories: 算法
tags:
- Leetcode
- Java
- List
---
题目描述

Given a linked list, determine if it has a cycle in it.

Follow up:
Can you solve it without using extra space?
<!--more-->
---

这题是监测一个单向链表中有没有环路，我们可以用2个结点，一个slow，一个fast，fast每次跑2步，如果fast跑到了null，说明链表有链尾，不可能有环。而在有环路的情况下，fast会比slow先进环，到slow也进环的时候，fast会一步一步逼近slow，他们必然会相遇，他们的相遇也证明了有环。

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head == null || head.next == null){
            return false;
        }

        ListNode slow = head;
        ListNode fast = head.next;

        while(slow != fast){
            if (fast == null || fast.next == null){
                return false;
            }
            slow = slow.next;
            fast = fast.next.next;
        }

        return true;
    }
}
```
