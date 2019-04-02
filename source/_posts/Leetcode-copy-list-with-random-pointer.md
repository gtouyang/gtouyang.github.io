---
title: Leetcode-copy-list-with-random-pointer
date: 2019-03-30 00:19:03
categories: 算法
tags:
- Leetcode
- Java
- List
---
题目描述

A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a deep copy of the list.
<!--more-->
---

深度复制一个带随机指针的单向链表，这一题难点在于next和random指针要指向新链表对应的结点，我们要怎么知道就链表中的random指向的结点再新链表中的哪个位置。这里我们采取的方案是将新链表插入在旧链表上拷贝，每一个拷贝结点都接在原结点的next，然后拷贝结点的next再指向原结点的next。举个例子，我们原有 `a→b→c` , 拷贝后变成 `a→a'→b→b'→c→c'`。然后我们可以知道拷贝结点的random应该指向原结点的random结点的拷贝，即 `random→next` 。最后我们把两个链表分开就完成了拷贝。

```java
/**
 * Definition for singly-linked list with a random pointer.
 * class RandomListNode {
 *     int label;
 *     RandomListNode next, random;
 *     RandomListNode(int x) { this.label = x; }
 * };
 */
public class Solution {
    public RandomListNode copyRandomList(RandomListNode head) {
        if(head == null){
            return null;
        }
        
        RandomListNode p = head;
        RandomListNode copy;

        //复制新链表，将新链表插入到原链表中，新节点在原节点下一个
        while(p != null){
            copy = new RandomListNode(p.label);
            copy.next = p.next;
            p.next = copy;
            p = copy.next;
        }

        //回到起点
        p = head;

        //以新链表的第一个结点作为头
        RandomListNode newHead = p.next;

        //将新结点的random指向原节点的random.next，要注意原节点的random是不是null
        while(p != null){
            copy = p.next;
            if (p.random == null){
                copy.random = null;
            }else{
                copy.random = p.random.next;
            }
            p = copy.next;
        }

        //回到起点
        p = head;

        //分离两个链表
        while(p != null){
            copy = p.next;
            p.next = copy.next;

            //注意链表最后一个结点时如果访问p.next.next会出错相当于null.next
            if(copy.next != null){
                copy.next = p.next.next;
            }
            p = p.next;
        }

        return newHead;
        
    }
}
```
