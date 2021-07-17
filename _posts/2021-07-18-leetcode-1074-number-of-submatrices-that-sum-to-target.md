---
title: "[LeetCode] 1074 Number of Submatrices That Sum to Target"
date: 2021-07-18 02:10:00 -0400
categories: PS
comments: true
tag : 

---

[문제보기](https://leetcode.com/problems/number-of-submatrices-that-sum-to-target/)  


```python
class Solution:
    def numSubmatrixSumTarget(self, matrix: List[List[int]], target: int) -> int:
        res = 0
        width = len(matrix[0])
        height = len(matrix)
    
        # 1. 누적값을 갖는 새로운 matrix를 만든다
        # 예를들어 s_matrix[i][j]에는, matrix[0][0] 부터 matrix[i][j] 까지의 모든 값을 합한 값이 저장된다
        s_matrix = copy.deepcopy(matrix)
        
        # 가로나 세로의 인덱스가 0인 경우는 따로 처리
        for j in range(1, width):
            s_matrix[0][j] += s_matrix[0][j-1]
            
        for i in range(1, height):
            s_matrix[i][0] += s_matrix[i-1][0]
        
        # 현재값 + 위에까지 누적된 값 + 왼쪽까지 누적된 값 - 왼쪽위까지 누적된 값 을 하면 쉽게 구할 수 있다
        for i in range(1, height):
            for j in range(1, width):
                s_matrix[i][j] += s_matrix[i-1][j] + s_matrix[i][j-1] - s_matrix[i-1][j-1]
                
    
        # 2. 본격적으로 target과 값이 일치하는 합을 가진 sub matrix를 찾아보자
        # 일단 탐색할 가로구간을 fix 해야 한다
        for w_start in range(0, width):
            for w_end in range(w_start, width):
                # 이렇게하면 가로 구간은 w_start ~ w_end 로 고정된 상태로 탐색을 할 수 있다
                # 이대로 아래로 내려가면서 target과 sum이 같은 sub matrix가 생길 수 있는지 찾는다
                # sum_dict은 이 가로 구간에 한해, 세로로 내려가면서 만들 수 있는 sum_dict의 케이스를 저장하는 역할을 맡는다
                # 예를 들어 sum_dict[2]가 3 이라면, 이 가로 구간 내에서는 합이 2이 되는 sub matrix가 총 3개가 있었다는 뜻이 된다
                # 만일 현재 시점에서 합이 7이고, target이 5라면, 위에서 합했던 sum_dict[2]의 케이스들을 빼주면 5가 될 수 있다는 의미이므로
                # res에 3을 더해주면 된다
                # (이해가 어렵다면 그림을 그려보면 좋다)
                sum_dict = collections.defaultdict(int)
                # 그리고 sub_sum[0] = 1로 초기화해야 딱 target과 sub이 일치하는 케이스에서 처리가 가능해진다 (일단 아래부터 보고 다시 보자)
                sum_dict[0] = 1
                
                # 본격적으로 세로 탐색을 해보자
                # h_start는 0이라 가정하므로 별도의 loop는 필요없다
                for h_end in range(0, height):
                    # 해당 가로구간에서 h_end 깊이까지의 합을 구한다
                    sub = s_matrix[h_end][w_end]
                    if w_start > 0:
                        sub -= s_matrix[h_end][w_start-1]
                    
                    # 만약 diff 만큼의 합을 만들 수 있는 케이스가 기존에 있었다면, 그 케이스를 뺐다고 생각하고 res에 값을 더해줄 수 있다
                    # 이 경우 sub == target인 경우도 포함해줄 수 있다 (앞에서 sum_dict[0]에 1을 미리 넣어줬기 때문)
                    diff = sub - target
                    res += sum_dict[diff]
                    
                    # 그리고 방금 만든 케이스로 sum_dict에 포함시키자
                    sum_dict[sub] += 1
        
        return res
```
