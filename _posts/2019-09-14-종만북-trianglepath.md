---
title: "[종만북] TRIANGLEPATH 삼각형 위의 최대 경로"
date: 2019-09-14 20:46:00 -0400
categories: PS
comments: true
tag : [동적계획법]



---

[문제보기](https://algospot.com/judge/problem/read/TRIANGLEPATH)

```java
import java.util.*;
import java.io.*;

public class Main{

  public static void main(String []args) throws IOException{
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    int t = Integer.parseInt(br.readLine());
    while(t-->0){
      int n = Integer.parseInt(br.readLine());
      int[][] map = new int[n][n];

      for(int i = 0 ; i < n ; i++ ){
        StringTokenizer st = new StringTokenizer(br.readLine());
        for(int j = 0 ; j <= i ; j++ ){
          map[i][j] = Integer.parseInt(st.nextToken());
        }
      }
			// 기본적으로 위에서부터 최적의 선택을 하면서 내려가는건 아무 의미가 없다
      // 맨 아래 구석에 999999 가 숨어있다면 다 소용없기 때문
      // 따라서 아래에서부터 최적의 선택을 하면서 올라가는게 좋다
      // i, j 까지 올라오는 동안 최대값
      int[][] d = new int[n][n];
			// 일단 맨 아랫단 애들은 시작점이므로 자기 자신이 최댓값이 된다.
      for(int i = 0 ; i < n ; i++ ){
        d[n-1][i] = map[n-1][i];
      }
			
      // 아랫줄부터 시작해서 서서히 올라온다
      for(int i = n-2 ; i >= 0 ; i-- ){
        // 열 번호는 그냥 앞에서부터 해줘도 상관 없다
        for(int j = 0 ; j <= i ; j++ ){
          // 해당 위치까지 올라오는 동안 최적의 결과는
          // 일단 자기 자신 더하고...
          // 바로 밑에 있는 애랑 그 오른쪽에 있는애 중에 큰 애를 선택하면 된다
          d[i][j] = map[i][j] + Math.max(d[i+1][j], d[i+1][j+1]);
        }
      }

      // 맨 위에 있는 애는 모든 숫자를 거쳐 최고의 결과만을 모은것임 
      System.out.println(d[0][0]);

    }
  }
}
```
