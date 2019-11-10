---
title: "[운영체제] 9. Virtual Memory"
date: 2019-11-10 12:00:00 -0400
categories: CS
comments: true

---

	
## Demand Paging ##
* 실제로 필요할 때 페이지를 메모리에 올리는것
	* I/O양의 감소 : 주로 사용되는 주소공간은 그렇게 넓지 않다. 특히 방어적인 소프트웨어라면 더더욱...그니까 필요한것만 올리면 그리 많지 않음 
	* 메모리 사용량 감소
	* 빠른 응답 시간
	* 더 많은 사용자 수용
* Valid / Invalid bit 의 사용
	* Invalid 의 의미 : 사용되지 않는 주소영역인 경우, 페이지가 물리적 메모리에 없는 경우
	* 처음에는 모든 페이지 엔드리가 invalid로 초기화
	* address translation 시에 invalid bit이 set되어 있으면 -> page fault -> 자동으로 OS로 제어권이 넘어감
	
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_01.png?raw=true) 

## Page Fault ##
• invalid page를 접근하면 MMU가 trap을 발생시킴(page fault trap)  
• Kernel mode로 들어가서 page fault handler가 invoke됨  
• 다음과 같은 순서로 page fault를 처리한다  
1. Invalid reference? ( eg. bad address, protection violation) => abort process
2. Get an empty page frame (빈게 없다면 뺏어라도 와라)
3. 해당 페이지를 디스크에서 메모리로 읽어온다  
	a. 디스크 I/O가 끝나기까지 이 프로세스는 CPU를 preempt당함 (block됨)  
	b. 디스크 read가 끝나면 page tables entry에 기록, valid/invalid bit = valid  
	c. ready queue에 프로세스를 다시 삽입(추후 dispatch)  
4. 이 프로세스가 CPU를 잡게되면 다시 running
5. 아까 중단되었던 instruction 을 재개

![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_02.png?raw=true) 

![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_03.png?raw=true) 




## Free frame이 없는 경우 ##
• Page replacement
- 어떤 프레임을 빼앗아올지 결정해야함
- 곧바로 사용되지 않을 페이지를 쫓아내는게 좋음
- 참고로 동일한 페이지가 여러번 메모리에서 쫓겨났다가 다시 들어올 수 있음  

• Replacement Algorithm
- page-fault rate를 최소화하는 것이 목표
- 알고리즘의 평가요소 : 주어진 페이지 참조 문자열에 대해 페이지폴트가 얼마나 나오는가?
- reference string의 예 : 1,2,3,4,1,2,5,1,2,3,4,5
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_04.png?raw=true) 
	
	
## Optimal Algorithm ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_05.png?raw=true) 

• 실제로 미래를 예측할 순 없기때문에 실현가능성은 없다  
• 그러나 이 이상 좋은 알고리즘은 없으므로(상한선) 타 알고리즘의 성능에 대한 비교분석에 사용되기 좋다  

## FIFO Algorithm ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_06.png?raw=true) 

• 메모리 크기를 늘려줘도 페이지폴트가 더 줄어들지 않는 기이한 현상이 일어날 수 있음 (FIFO Anomaly)

## LRU Algorithm ##

![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_07.png?raw=true) 

## LFU Algorithm ##
• LFU : 참조횟수가 가장 적은 페이지를 지우자  
• 최저 참조횟수가 동률인 페이지가 여럿 있을땐? 임의로 선정하든가(디폴트), 성능향상을 위해 걔 중 가장 오래전에 참조된 페이지를 지우게 구현하든가..  

• 장단점  
- LRU처럼 직전 참조 시점만 보는것이 아니라 장기적인 시간규모를 보기 때문에 page의 인기도를 좀 더 정확히 반영할 수 있음
- 참조시점의 최근성을 반영못해
- LRU보다 구현이 복잡
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_08.png?raw=true) 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_09.png?raw=true) 



## 다양한 캐싱 환경 ##
### 캐싱 기법  ###
- 한정된 빠른공간(=캐시)에 요청된 데이터를 저장해 두었다가 후석 요청시 캐시로부터 직접 서비스하는 방식
- paging system외에도 cache memory, buffer caching, web caching등 다양한 분야에서 사용  

### 캐시 운영의 시간 제약 ###
- 교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없음
- Buffer caching이나 web caching의 경우 : O(1) ~ O(log N) 정도까진 허용
- Paging system인 경우 : 
	page fault일때만 OS가 관여  
	페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없음 - 왜냐하면 이건 하드웨어적으로 처리되는거지 OS에게 애초에 제어권이 넘어가질X - 즉 페이지폴트가 났을때에는 OS가 관여하니까 알 수 있지만.. 이미 메모리에 있을때는 OS는 그런일이 일어난줄도 모름 ㅠㅠ 운영체제는 반쪽짜리 정보밖에 갖고있질 않음
	O(1)인 LRU의 list조작 조차 불가능함    
	그래서 등장한게 아래 시계알고리즘~  
	
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_10.png?raw=true) 
	
## Clock Algorithm ##
- LRU의 근사 알고리즘
- 여러 명칭 : Second chance algorithm, NUR(NRU) - not recently used
- Reference bit를 사용해서 교체 대상 페이지를 선정(circular list)
- reference bit가 0인것을 찾을때까지 포인터를 하나씩 앞으로 이동
- 포인터 이동중 reference bit 가 1인것은 모두 0으로 바꿔가며
- Reference bit이 0인것을 찾는순간 그 페이지를 교체
- 한바퀴 되돌아와서도 0이면(=second chance) 그때는 replace당하게 됨
- 자주 사용되는 페이지라면 second chance가 올 때 1이겠쥬?
- 시계 알고리즘의 개선
	1. reference bit 이랑 modified bit(dirty bit)을 함께 사용하기
	2. reference bit = 1 : 최근에 참조된 페이지
	3. modified bit = 1 : 최근데 변경된 페이지(I/O를 동반하는 페이지) - 만약 얘가 쫓겨나게되면 modified bit을 보고 swap area로 쫓아낼 때 변경사항을 반영해야함

![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_11.png?raw=true) 
	
	
## Page Frame의 Allocation ##
- Allocation Problem : 각 프로세스에 얼마만큼의 page frame을 할당할것인가?
- Allocation 의 필요성  
	메모리 참조 명령어 수행 시 명령어, 데이터 등 여러 페이지를 동시 참조 - 즉 명령어 수행을위해 최소한 할당되어야하는 프레임의 수가 있음
	루프를 구성하는 페이지들은 한꺼번에 allocate되는것이 유리 - 최소한의 allocaiton이 없으면 매 루프마다 페이지폴트..
- Allocation Scheme : Equal allocation / Proprtional allocation / Priority allocation
	

## Global vs Local Replacement ##
### Global Replacement ###
- Replace시 다른 프로세스에 할당된 프레임을 빼앗아올 수 있다.
- 프로세스별 할당량을 조절하는 또다른 방법임
- FIFO, LRU, LFU 등의 알고리즘을 global replacement로 사용시에 해당
- Working set, PFF 알고리즘 사용
	
### Local Replacement ###
- 자신에게 할당된 프레임내에서만 replacement
- FIFO, LRU, LFU 등의 알고리즘을 프로세스별로 운영시
	
	
## Thrashing ##
- 프로세스의 원활한 수행에 필요한 최소한의 페이지프레임수를 할당받지 못한 경우 발생
- page fault rate이 매우 높아짐
- CPU utilization이 낮아짐
- OS는 CPU utilization이 낮아지니까 MPD(Multiprogramming degree)를 높여야 한다고 판단
- 또다른 프로세스가 시스템에 추가됨
- 프로세스 당 할당된 프레임의 수가 더욱 감소
- 프로세스는 페이지의 swap in / swap out으로 매우 바쁨
- 대부분의 시간에 CPU는 한가함
- 낮은 처리율 ^^!
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_12.png?raw=true) 
	 
	
## Working-Set Model ##

### Locality of reference  ###
- 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조한다  
- 집중적으로 참조되는 해당 페이지들의 집합을 working set이라 한다  

### Working-set Model ###
- Locality에 기반하여 프로세스가 일정시간동안 원할하게 수행되기 위해 한꺼번에 메모리에 올라와있어야 하는 페이지들의 집합을 워킹셋이라 정의  
- 워킹셋 모델에서는 프로세스의 워킹셋 전체가 메모리에 올라와있어야 수행되고 그렇지 않을 경우 모든 프레임을 반납한 후 전체 프로세스가 swap out(suspended)
- 스레싱을 방지함
- MPD를 결정함
	
## Working-Set Algorithm ##
- 어떤 페이지들이 워킹셋을 이룰지 미리 알면 얼마나 좋으련만.. 현실은 그렇지 않다.
- 늘 그랬듯이 과거를 보며 예측을 하는 것이다
- 과거 델타시간(윈도우)동안 참조된 페이지들을 워킹셋이라 친다

![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_13.png?raw=true) 
	
### 워킹셋 알고리즘 ###
- 프로세스들의 워킹셋 사이즈의 합이 페이지 프레임의 수보다 큰 경우?
- 일부 프로세스를 swap out시켜 남은 프로세스의 워킹셋을 우선적으로 충족시켜보자(MPD낮추기)
- 워킹셋을 다 할당하고도 페이지 프레임이 남는다면?
- Swap out 되었던 프로세스에게 워킹셋을 할당(MPD를 키운다)
	
### 윈도우 사이즈 ###
- 워킹셋을 제대로 탐지하기 위해서는 윈도우 사이즈를 잘 결정해야함
- 델타값이 너무 작으면 locality set을 모두 수용하지 못할 우려
- 델타값이 너무 크면 여러규모의 locality set을 수용
- 델카값이 무한대라면 전체 프로그램을 구성하는 페이지가 워킹셋으로 간주됨


## PFF(Page-Fault Frequency) Scheme ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/09_14.png?raw=true) 

- 페이지폴트 비율의 상한값과 하한값을 둔다
- 상한값을 넘는다면 프레임을 더 할당한다(인력투입!)
- 하한값 이하라면 프레임 수를 줄인다(일하기 편하지?)
- 빈 프레임이 없으면 일부 프로세스를 swap out


## Page Size의 결정 ##
### 페이지 사이즈를 감소시키면? ###
- 페이지 수 증가 
- 페이지 테이블 크기 증가 
- 내부단편화 감소 
- Disk transfer의 효율성 감소(Seek/rotation vs transfer) 
- 필요한 정보만 메모리에 올라와 메모리 이용이 효율적(locality의 활용 측면에서는 좋지 않음)
- Trend : larger page size


------

본 포스팅은 [이화여대 반효경 교수님의 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323&ar=pop)를 기반으로 만들어졌습니다.  

문제시 삭제하도록 하겠습니다.  
