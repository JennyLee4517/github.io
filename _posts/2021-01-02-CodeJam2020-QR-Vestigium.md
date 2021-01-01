---
title: "[CodeJam] Qualification Round 2020 Vestigium"
date: 2021-01-02 04:05:00 -0400
categories: PS
comments: true
tag : 

---

[문제보기](https://codingcompetitions.withgoogle.com/codejam/round/000000000019fd27/000000000020993c)  


```java
import java.io.*;
import java.util.*;

public class Solution {

    public static void main(String[] args) throws Exception {

		Scanner sc = new Scanner(System.in);
		PrintWriter pw = new PrintWriter(System.out);

        int testCases = sc.nextInt();
        for (int testCase = 1; testCase <= testCases; testCase++) {
            int n = sc.nextInt();
            int k = 0;
            
            // get k (좌상단부터 우하단까지 대각선값의 합)
            int[][] arr = new int[n][n];
            for(int i = 0 ; i < n ; i++) {
                for(int j = 0 ; j < n ; j++) {
                    arr[i][j] = sc.nextInt();
                    if (i == j) {
                        k += arr[i][j];
                    }
                }
            }
            
            // get r (중복되는 원소를 가진 행의 갯수)
            int r = 0;
            for(int i = 0 ; i < n ; i++) {
                Set<Integer> nums = new HashSet();
                for(int j = 0 ; j < n ; j++) {
                    if (nums.contains(arr[i][j])) {
                        r++;
                        break;
                    } else {
                        nums.add(arr[i][j]);
                    }
                }
            }
            
            // get c (중복되는 원소를 가진 열의 갯수)
            int c = 0;
            for(int j = 0 ; j < n ; j++) {
                Set<Integer> nums = new HashSet();
                for(int i = 0 ; i < n ; i++) {
                    if (nums.contains(arr[i][j])) {
                        c++;
                        break;
                    } else {
                        nums.add(arr[i][j]);
                    }
                }
            }

            pw.printf("Case #" + testCase + ": %d %d %d\n", k, r ,c);
            pw.flush();
        }

        pw.close();
        sc.close();
    }
}

```
