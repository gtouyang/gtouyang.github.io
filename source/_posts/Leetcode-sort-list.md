---
title: sort-list
date: 2019-03-29 22:25:15
categories: 算法
tags:
- Leetcode
- List
- Sort
- Java
---
题目描述

Sort a linked list in O(n log n) time using constant space complexity.
<!--more-->
---

这里用的是合并排序，将链表一分为二，然后将左右链表头结点按大小顺序接入新链表中

```java
public class Solution {
// 合并排序

    private ListNode findMiddle(ListNode head) {
        //TODO 找出链表的中部
        ListNode slow = head, fast = head.next;
        while(fast != null && fast.next != null){
            fast = fast.next.next;  //fast每次跑2格
            slow = slow.next;       //slow每次跑1格
        }
        return slow;        //跑完后slow刚好在中间
    }

    private ListNode merge(ListNode head1, ListNode head2){
        //TODO 将两个链表从小到大接入新链表中
        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;
        while (head1 != null && head2 != null){

            //比较2个链表中的第一个节点
            if (head1.val < head2.val) {
                tail.next = head1;      //若head1小就接入head1，第一个链表长度减一
                head1 = head1.next;
            }else{
                tail.next = head2;      //若head2小就接入head2，第二个链表长度减一
                head2 = head2.next;
            }
            tail = tail.next;       //获得新链表的链尾(其实不一定是链尾，不过是当前已确定的部分的最后一个，链后面可能还有节点，只是还没确定)
        }

        //最后判断哪个链表还有剩，有则接上
        if(head1 != null){
            tail.next = head1;
        }else{
            tail.next = head2;
        }

        //新链表去头返回(头是临时创建的，方便操作而已)
        return dummy.next;
    }
    public ListNode sortList(ListNode head) {
        //TODO 合并排序

        //如果链表只有一个节点或为空，则返回自己
        if (head == null || head.next == null){
            return head;
        }

        //获得链表中部
        ListNode mid = findMiddle(head);

        //合并排序右链表
        ListNode right = sortList(mid.next);

        //剪断链表
        mid.next = null;

        //合并排序左链表
        ListNode left = sortList(head);

        //合并左右链表
        return merge(left, right);
    }
}
```

