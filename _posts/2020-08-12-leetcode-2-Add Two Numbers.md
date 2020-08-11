---
title: "[LeetCode] 2 Add Two Numbers"
date: 2020-08-12 01:50:00 -0400
categories: PS
comments: true
tag : [Graph]

---

[문제보기](https://leetcode.com/problems/add-two-numbers/)  


```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */

public class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {

        ListNode first = new ListNode();
        ListNode ans = first;
        boolean over = false;

       while(true) {
           
           int sum = 0;
           
           if(l1 != null) {
                sum += l1.val;   
           }
           
           if(l2 != null) {
               sum += l2.val;
           }

           if(over) {
               sum++;
               over = false;
           }
               
           if(sum >= 10) {
               sum -= 10;
               over = true;
           }

           ans.val = sum;

           if(l1 != null) {
               l1 = l1.next;
           }
           
           if(l2 != null) {
               l2 = l2.next;
           }
           
           if(l1 == null && l2 == null) {
               break;
           }

           ans.next = new ListNode();
           ans = ans.next;
       }
    
        if(over) {
            ans.next = new ListNode(1);
        }

       return first;
    }

}


```
