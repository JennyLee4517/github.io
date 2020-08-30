---
title: "[LeetCode] 3 Longest Substring Without Repeating Characters"
date: 2020-08-31 01:50:00 -0400
categories: PS
comments: true
tag : [brute force]

---

[문제보기](https://leetcode.com/problems/longest-substring-without-repeating-characters/)  


```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int len = s.length();
        int max = 1;
        
        for(int i = 0 ; i < len ; i++) {
            Set<Character> set = new HashSet<>();
            set.add(s.charAt(i));
            
            for(int j = i+1 ; j < len ; j++) {
                // System.out.println("i: " + i + ", j: " + j);
                // System.out.println("size: " + set.size() );
                Character next = s.charAt(j);
                // System.out.println("next: " + next + " , contains: " + set.contains(next));
                if(!set.contains(next)) {
                    // System.out.println("new char! " + next);
                    set.add(next);
                    max = Math.max(max, set.size());
                } else {
                    break;
                }
            }

        }
        
        return Math.min(len, max);
    }
}

```
