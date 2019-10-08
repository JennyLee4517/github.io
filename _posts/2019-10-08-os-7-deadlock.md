---
title: "[운영체제] 7. Deadlock"
date: 2019-10-08 17:45:00 -0400
categories: CS
comments: true

---

### Deadlock ###
* 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태

### Resource(자원) ###
* 하드웨어, 소프트웨어 등을 포함하는 개념
* 예) I/O device, CPU cycle, memory space, semaphore 등
* 프로세스가 자원을 사용하는 절차
- Request > Allocate > Use > Release

#### deadlock ex1 ####
* 시스템에 2개의 tape drive가 있다
* 프로세스 P1과 P2각각이 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있다.

#### deadlock ex2 ####
* Binary semaphores A & B  
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/07_01.png?raw=true)
 

### Deadlock 발생의 4가지 조건 ###
1. Mutual Exclusion (상호배제)
- 매순간 하나의 프로세스만이 자원을 사용할 수 있음)

2. No Preemption (비선점)
- 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음

3. Hold and wait (보유대기)
- 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유자원을 놓지 않고 계속 갖고있음

4. Circular wait(순환대기)
- 자원을 기다리는 프로세스간에 사이클이 형성되어야 함
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/07_02.png?raw=true)


### Resource-Allocation Graph (자원할당 그래프) ###
* 데드락이 걸렸는지 알아보기 위해 그려보는 것
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/07_03.png?raw=true)

#### 데드락인지 아닌지 어떻게 구분할까? ####
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/07_04.png?raw=true)

* 일단 그래프에 사이클이 없으면 데드락이 아니다.
* 만약 사이클이 있다면?
1. if only one instance per resource type, then **deadlock** - 사이클 당 인스턴스가 한개뿐일 때 (왼쪽 그림)
2. if several instances per resource type, **possibiity** of deadlock (오른쪽 그림)

### Deadlock의 처리 방법 ###
* Deadlock Prevention  
자원 할당 시 데드락의 4가지 필요조건 중 어느 하나가 만족되지 않도록 하자

* Deadlock Avoidance  
자원 요청에 대한 부가적인 정보를 이용해서 데드락의 가능성이 없는 경우에만 자원을 할당  
시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원을 할당

* Deadlock Detection and recovery  
데드락 발생은 허용하되 그에 대한 감지 루틴을 두어 데드락 발견시 리커버되도록

* Deadlock Ignorance  
데드락을 시스템이 책임지지 않는 것  
유닉스를 포함한 대부분의 OS가 채택함 

#### 각각의 방법에 대해 더 자세히 알아보자 ####

### Deadlock Prevention ###

**Mutual Exclusion**
* 공유해서는 안되는 자원의 경우 반드시 상호배제가 성립해야 함   

**Hold and Wait**
* 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 한다(all or nothing)
  * 방법1 : 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법(wait 할일이 없음 - 단 자원의 비효율성)
  * 방법2 : 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청  

**No Preemption**
* 프로세스가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원은 남에게 선점될 수 있어야 함
* 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작된다
* state를 쉽게 save하고 restore할 수 있는 자원에서 주로 사용(CPU, memory)  

**Circular Wait**
* 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원을 할당
* 즉 자원 선점에도 다 순서가 있어서, 1을 못먹으면 2을 먹는것 자체가 불가하게 해버리는것…!
* 예를 들어 순서가 3인 자원 Ri를 보유중인 프로세스가 순서가 1인 자원 Rj를 할당받기 위해서는 우선 Ri를 release해야 한다.  

**Deadlock Prevention의 문제점**
* Utilization의 저하, throughput 감소, starvation 문제


### Deadlock Avoidance ###
**Deadlock avoidance**
* 자원 교청에 대한 부가정보를 이용해서 자원 할당이 deadlock으로부터 안전한지를 동적으로 조사해서 안전한 경우에만 할당
* 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법임
* 평생에 쓸 자원들을 미리 선언(어떤 자원을 & 몇개까지)하여 최악의 경우를 피하는것  

**Safe state**
* 시스템 내의 프로세스들에대한 safe sequence가 존재하는 상태  

**Safe sequence**
* 프로세스의 sequence<P1, P2, … , Pn>이 safe하려면 Pi(1 <= i <= n)의 자원 요청이 “가용 자원 + 모든 Pj( j<i ) 의 보유자원”에 의해 충족되어야 함
* 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장
1. Pi의 자원 요청이 즉시 충족될 수 없으면 모든 Pj(j<i)가 종료될 때까지 기다린다
2. Pi-1이 종료되면 Pi의 자원요청을 만족시켜 수행한다  
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/07_05.png?raw=true)


**시스템이 safe state에 있으면**
* no deadlock

**시스템이 unsafe state에 있으면**
* possibility of deadlock

**Deadlock Avoidance**
* 시스템이 unsafe state에 들어가지 않는 것을 보장
* 두가지 경우의 회피 알고리즘
1. **Single Instance** per resource types : Resource Allocation Graph algorithm 사용
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/07_06.png?raw=true)  
진짜 재수없는 경우를 가정하는것임 

2. **Multiple instances** per resource types : Banker’s Algorithm 사용
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/07_07.png?raw=true)  
need = max - allocation  
availble = total - allocation  
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/07_08.png?raw=true)  

즉, need부분이 available로 커버될 때만 할당해줌 (need <= availble)  
그니까 당장 요청하는게 need보다 작아도, 걔의 need > available이면 한개도 안줌  
아무리 욕심부려도 현재 갖고있는걸로 다 커버되는 애한테만 요청을 들어줌 

즉, p1, p3, p4, p2, p0 순서로 요청된다면 이 상황은 데드락이 절대 생기지 않는 순서가 된다. 따라서 safe state이다.   
 
 
### Deadlock Detection and recovery ###
* 데드락이 뭐 그렇게 흔하게 일어나는것도 아닌데 뭐…
* 닥치면 수습하자!

**Deadlock Detection**
* Resource type당 single instance인 경우  
	* 자원할당 그래프에서의 사이클이 곧 데드락을 의미
* Resource type당 multiple instance인 경우  
	* 은행원 알고리즘과 유사한 방법을 활용해서 생각

**Wait-for graph 알고리즘**
* Resource type당 single instance인 경우
* Wait-for graph
	* 자원할당 그래프의 변형
	* 프로세스만으로 node 구성
	* Pj가 가지고 있는 자원을 Pk가 기다리는 경우를 Pk->Pj로 표현
* 알고리즘
	* Wait-for graph에 사이클이 존재하는지를 주기적으로 조사 O(n^2)
	* n^2이 걸리는 이유.. 화살표를 다 따라가야함 노드가 n개면 화살표는 최대 n(n-1) 왜냐하면 각각의 노드가 다른 전부에게 화살표가 나간다면 n-1개 이므로
 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/07_09.png?raw=true)  

낙관적으로 보자. P0는 요청이 받아들여지며 가진 자원을 반납할것이다 -> P2도 요청이 받아들여지며 자원을 반납할것이다 -> …  나머지도 끝까지 모두 가능  

**만약 P2가 C를 하나 더 요구한다면?**
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/07_10.png?raw=true)  

P0 후에도 P2를 만족시켜줄 순 없음 -> 데드락 발견!-> 그럼 리커버리를 해보자  

**Recovery**
* Process termination
	* Abort all deadlocked processes
	* Abort one process at a time until the deadlock cycle is eliminated
* Resource Preemption
	* 비용을 최소하 할 victim의 선정
	* safe state 로 rollback하여 process를 재시작
	* Starvation문제 : 동일한 프로세스가 계속해서 victim으로 선정되는 경우, cost factor에 rollback 횟수도 같이 고려해서 공정하게..  

### Deadlock Ignorance ###
**Deadlock이 일어나지 않는다고 생각하고 아무런 조치도 취하지 않음**
* 데드락이 매우 드물게 발생하므로 그것에 대한 조치 자체가 더 큰 오버헤드일 수 있다
* 만약 시스템에 데드락이 발생한 경우 시스템이 비정상적으로 작동하는것을 사람이 느낀 후 직접 프로세스를 죽이는 등의 방법으로 대처
* 유닉스, 윈도우 등 대부분의 범용 OS가 채택


------

본 포스팅은 [이화여대 반효경 교수님의 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323&ar=pop)를 기반으로 만들어졌습니다.  

문제시 삭제하도록 하겠습니다.  
