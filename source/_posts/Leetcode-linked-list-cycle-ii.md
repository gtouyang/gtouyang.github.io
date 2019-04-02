---
title: Leetcode-linked-list-cycle-ii
date: 2019-03-30 00:00:17
categories: 算法
tags:
- Leetcode
- Java
- List
---
题目描述

Given a linked list, return the node where the cycle begins. If there is no cycle, returnnull.

Follow up:
Can you solve it without using extra space?
<!--more-->
---

当链表有环时，fast会比slow先一步入环，当二者都进入环后，fast每回合会接近slow一步，所以他们必然相遇当slow和fast相遇后，假设slow走a步入环，再走b步与fast相遇，假设fast多走了c步，又因为fast走的是slow的两倍，那么有 2(a+b) = a+2b+c，即a=c。因为此时slow还有c步到环的入口，而head离入口a步，因a=c，所以他们必然相遇且在环的入口相遇。

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

import java.util.*;

public class Solution {
    public ListNode detectCycle(ListNode head) {

        if (head == null || head.next == null) {
            return null;
        }

        ListNode slow = head.next;
        ListNode fast = head.next.next;

        while(slow != fast){
            if(fast == null || fast.next == null){
                return null;
            }
            slow = slow.next;
            fast = fast.next.next;
        }

        while(head != slow){
            head = head.next;
            slow = slow.next;
        }
        return head;
	}
}
```
