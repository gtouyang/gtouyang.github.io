---
title: reorder-list
date: 2019-03-29 23:32:29
categories: 算法
tags:
- Leetcode
- Java
- List
---
题目描述

Given a singly linked list *L: L 0→L 1→…→L n-1→L n*,
reorder it to: *L 0→L n →L 1→L n-1→L 2→L n-2→…*

You must do this in-place without altering the nodes' values.

For example,
Given{1,2,3,4}, reorder it to{1,4,2,3}.
<!--more-->
---
这一题要求将链表按新的规则重新排列，将倒数第i个结点放在正数第i个结点后面，如果我们一个一个放的话代码比较难实现，也比较难阅读。我们可以把链表从中间分开两边，例如分成{1,2}和{3,4},然后将后面的链表倒序，变成{4,3}然后将两个链表一个一个合起来就行。

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
    public void reorderList(ListNode head) {

        if(head == null || head.next == null){
            return;
        }

        //找到链表中间
        ListNode mid = findMiddle(head);

        //将链表中间后面的部分倒序
        ListNode tail = reverse(mid.next);

        //切开两个链表
        mid.next = null;

        //合并链表
        merge(head, tail);

    }

    //找到链表的中间
    private ListNode findMiddle(ListNode head) {
        if (head == null) {
            return head;
        }
        ListNode slow = head;
        ListNode fast = head.next;

        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }

        return slow;
        
    }

    //将链表倒序
    private ListNode reverse(ListNode head){
        ListNode newHead = null;
        while(head != null){
            ListNode temp = head.next;
            head.next = newHead;
            newHead = head;
            head = temp;
        }
        return newHead;
    }

    //合并2个链表
    public void merge(ListNode head1, ListNode head2){
        Boolean addHead1 = true;
        ListNode dummy = new ListNode(0);
        while(head1 != null && head2 != null){
            if(addHead1){
                dummy.next = head1;
                head1 = head1.next;
                addHead1 = false;
            }else{
                dummy.next = head2;
                head2 = head2.next;
                addHead1 = true;
            }
            dummy = dummy.next;
        }
        if(head1 != null){
            dummy.next = head1;
        }else{
            dummy.next = head2;
        }

    }

}
```
