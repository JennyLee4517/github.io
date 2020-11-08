---
title: "[LeetCode] 4 Median of Two Sorted Arrays"
date: 2020-11-06 13:05:00 -0400
categories: PS
comments: true
tag : 

---

[문제보기](https://leetcode.com/problems/median-of-two-sorted-arrays/)  


```java
class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        
        int sizeSum = nums1.length + nums2.length;
        
        // 총합이 짝수
        if (sizeSum % 2 == 0) {
            
            int goalIdx1 = sizeSum / 2;
            int goalIdx2 = goalIdx1 - 1;
            
            int idx1 = 0;
            int idx2 = 0;
            List<Integer> arr = new ArrayList<>();
            
            while (arr.size() < goalIdx1 + 1) {
                if (idx1 >= nums1.length) {
                    arr.add(nums2[idx2]);
                    idx2++;
                    continue;
                }
                if (idx2 >= nums2.length) {
                    arr.add(nums1[idx1]);
                    idx1++;
                    continue;
                }
                if (nums1[idx1] < nums2[idx2]) {
                    arr.add(nums1[idx1]);
                    idx1++;
                } else {
                    arr.add(nums2[idx2]);
                    idx2++;
                }
                // System.out.println("arr: " + arr + " idx1: " + idx1 + " idx2: " + idx2);
                
            }
            
            return (arr.get(goalIdx1) + arr.get(goalIdx2)) / 2.0;
            
        // 총합이 홀수
        } else {
            
            int goalIdx = (sizeSum - 1) / 2;
            
            int idx1 = 0;
            int idx2 = 0;
            List<Integer> arr = new ArrayList<>();
            
            while (arr.size() < goalIdx + 1) {
                if (idx1 >= nums1.length) {
                    arr.add(nums2[idx2]);
                    idx2++;
                    continue;
                }
                if (idx2 >= nums2.length) {
                    arr.add(nums1[idx1]);
                    idx1++;
                    continue;
                }
                if (nums1[idx1] < nums2[idx2]) {
                    arr.add(nums1[idx1]);
                    idx1++;
                } else {
                    arr.add(nums2[idx2]);
                    idx2++;
                }
            }
            
            return arr.get(goalIdx);
        }
    }
}

```
