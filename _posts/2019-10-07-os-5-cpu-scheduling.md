---
title: "[운영체제] 5. CPU Scheduling"
date: 2019-10-07 19:00:00 -0400
categories: CS
comments: true

---
 
### CPU burst & I/O burst ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_01.png?raw=true)

* 사람과의 작업이 많은 경우가 주로 이런 형태임 (I/O가 많이 끼어듦 - cpu burst가 짧아지고 횟수는 커짐 - 난도질..?)
 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_02.png?raw=true)

* interactive job이 답답하지 않게 해주는것이 포인트
* 얼만큼 시간을 주고 뺏을거냐?
### 프로세스의 특성 분류 ###
* I/O bound process
	* CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
	* many short CPU bursts
* CPU-bound process
	* 계산 위주의 job
	* few very long CPU bursts
### CPU Scheduler & Dispatcher ###
* CPU Scheduler
	* ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스 고르기
* Dispatcher
	* CPU 제어권을 CPU sheduler에 의해 선택된 프로세스에게 넘긴다
	* 이 과정을 context switch라고 한다
* CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다
1. running -> blocked (예 : I/O 요청하는 시스템 콜)
2. running -> ready (예 : 타이머 인터럽트)
3. blocked -> ready (예 : I/O 완료 후 인터럽트)
4. Terminate
* 1, 4는 자진반납 - nonpreemptive(비선점형)
* 2, 3은 강제반납 - preemptive(선점형)
 
 
 
### Scheduling Criteria ###
* 시스템 입장에서
	* CPU utilization(이용률)
		* keep the CPU as busy as possible
	* Throughput(처리량)
		* number of processes that complete their execution per time unit
* 프로세스 입장에서
	* Turnaround time(소요시간, 반환시간)
		* amount of time to execute a particular process
		* CPU를 선점한 뒤 IO하러 나갈때까지 걸린 총 시간
	* Waiting time(대기시간)
		* amount of time a process has been waiting in the ready queue
		* 기다린 시간을 모두 합친 개념
	* Response time(응답시간)
		* amount of time it takes from when a request was submitted until the first response is produced, not output(for time-sharing environment)
		* 처음으로 CPU를 얻을때까지 기다린 시간
### FCFS (First-Come First-Served) - 비선점형 ###
* 먼저 온 순서대로 처리해준다
* 비효율적인 예
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_03.png?raw=true)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_04.png?raw=true)

* 앞에 어떤 프로세스가 있느냐에 따라 효율성의 기복이 큼


### SJF (Shortest Job First) ###
* 각 프로세스의 다음번 CPU birst time을 가지고 스케쥴링에 활용
* CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄
* Two schemes:
	* 비선점형
		* 일단 CPU를 잡으면 완료될때까지 CPU를 선점당하지 않음
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_05.png?raw=true)
	* 선점형
		* 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 새로운 프로세스가 도착하면 CPU를 빼앗긴다
		* Shortest-Remaining-Time-First(SRTF)라고도 함
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_06.png?raw=true)

* SRTF is optimal
	* 주어진 프로세스들에 대하여 최소 평균 대기시간을 보장함
* 단점
	* Starvation(기아현상) : CPU 사용시간이 긴 애들은 영원히 CPU를 못 쓸 수도 있음
	* 다음번 CPU Burst time의 예측의 부정확성 : 추정만 가능할 뿐, 과거의 CPU burst time을 참고할 뿐..
		* exponentional averaging
n+1번째의 예측치 = a*(n번째의 실제사용시간) + (1-a)*(n번째의 예측치)
(단, 0 <= a <= 1)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_07.png?raw=true)

최근의 실제사용시간이 가장 높은 가중치를 갖는다  


### Priority Scheduling ###
* 각각의 프로세스에 우선순위 숫자가 부여된다
* 가장 높은 우선순위를 가진 프로세스에게 CPU 할당
* SJF는 일종의 우선순위 스케줄링임
* 문제점 : Starvation
* 해결법 : Aging - 시간이 지남에 따라 우선순위를 높여준다(노화 > 요실금)

### Round Robin (RR) ###
* 각 프로세스는 동일한 크기의 할당시간을 가짐(10-100ms)
* 할당 시간이 지나면 프로세스는 선점당하고 레디큐 제일 뒤로
* n개의 프로세스가 레디큐에 있고 할당시간이 q인 경우 -> 어떤 프로세스도 (n-1)*q 이상 기다리지 않는다
* 응답시간이 짧아진다는점, 버스트 예측하는 과정도 필요없음
* 단, 평균 turnaround 시간은 길다
* Performance
	* q가 클 때 : FCFS
	* q가 작을 때 : 문맥교환 오버헤드가 커진다
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_08.png?raw=true)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_09.png?raw=true)

 
* RR… 공정하긴 한데 정말로 효율적인 방식인걸까???
* 진짜진짜 사용시간이 짧은애는 조금 더 호의를 받을 순 없는걸까?
* 그래서 나온게…. 👇

### Multilevel Queue ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_10.png?raw=true)

* 태어난 출신에 따라 줄을 선다(영원히 극복 못할 신분의 차이)
* Ready queue를 여러개로 분할
	* foreground - interactive
	* background (batch - no human interaction)
* 각 큐는 독립적인 스케줄링 알고리즘을 가짐
	* foreground - RR
	* background - FCFS
* 큐에 대한 스케줄링이 필요
	* Fixed proirity scheduling
		* serve all from foreground then from background
		* starvation될 가능성있음 - 우선순위 노픈 줄이 비지 않으면…아랫줄은 영영 기다려야 한다
	* Time slice
		* 각 큐에 CPU time을 적절한 비율로 할당
		* 예) 80% to foreground in RR, 20% to background in FCFS
 
### Multilevel Feedback Queue ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_11.png?raw=true)
 

* 신분이 바뀔 수 있다
* 신분에 관계없이 일단은 1등시민 취급을 해줌 -> 주어진 타임퀀텀내에 작업이 안끝나면 -> 아직도 안끝나네? 너 강등
* 예
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_12.png?raw=true)

 
 
여태까지는 CPU가 하나뿐일 때였고.. 그 외에도 특별한 케이스에 대한 스케줄링 방식을 알아보자  
 
### Multiple-Processor Scheduling ###
* CPU가 여러개인 경우 스케줄링은 더욱 복잡해짐
* Homogeneous processor인 경우
	* 큐에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다
	* 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐
* Load sharing
	* 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 매커니즘이 필요
	* 별개의 큐를 두는 방법(화장실 문앞마다 줄서기) vs 공동 큐를 사용하는 방법(한줄서기)
* Symmetric Multiprocessing(SMP)
	* 모든 CPU가 대등함
	* 각 프로세서가 각자 알아서 스케줄링 결정
* Asymmetric multiprocessing
	* 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고(컨트롤) 나머지 프로세서는 거기에 따름(일꾼)
### Real-Time Scheduling ###
* Hard real-time systems
	* 정해진 시간 안에 반드시 끝내도록 스케줄링
* Soft real-time computing
	* 데드라인을 꼭 보장하진 않아도 그냥 일반 프로세스에 비해 높은 우선순위만 갖게 한다
### Thread Scheduling###
* 스레드 : 하나의 프로세스 안에 CPU 수행단위가 여러개인것
* Local Scheduling
	* 유저레벨 스레드의 경우 사용자 수준의 스레드 라이브러리에 의해 스케줄링
* Global Scheduling
	* 커널레벨 스레드의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 스레드를 스케줄할지 결정
 
### Algorithm Evaluation ###
* Queueing models
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/05_13.png?raw=true)
* 이론적인 모델
* 확률 분포로 주어지는 도착률과 처리율을 통해 각종 performance index 값을 계산
* 구현 & 성능측정 (Implementation & Measurement)
	* 실제 시스템에 알고리즘을 구현하여 실제 작업에 대해 성능을 측정비교
	* 요즘은 이걸 더 의미있게 생각한다
* 모의 실험
	* 알고리즘을 모의 프로그램으로 작성 후 trace(테스트케이스)를 입력으로 하여 결과 비교

------

본 포스팅은 [이화여대 반효경 교수님의 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323&ar=pop)를 기반으로 만들어졌습니다.  

문제시 삭제하도록 하겠습니다.  
